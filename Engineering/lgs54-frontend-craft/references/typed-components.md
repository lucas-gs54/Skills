# Typed Components — TypeScript Patterns, Polymorphic Components, Variant Discrimination

TypeScript that catches errors at compile time, enables IDE autocomplete, and documents component APIs.

---

## Primitive Component Props — Strict, No Escape Hatches

### Box Primitive
```tsx
// primitives/Box.tsx
import { forwardRef, type HTMLAttributes, type ElementType } from 'react';
import { type SpaceToken, type RadiusToken, type ElevationToken, type ColorToken } from '../tokens';

export interface BoxProps extends Omit<HTMLAttributes<HTMLDivElement>, 'as' | 'color'> {
  as?: ElementType;
  asChild?: boolean;
  /** Layout */
  display?: 'block' | 'inline' | 'flex' | 'inline-flex' | 'grid' | 'none';
  /** Spacing — token only */
  p?: SpaceToken;
  px?: SpaceToken;
  py?: SpaceToken;
  pt?: SpaceToken;
  pr?: SpaceToken;
  pb?: SpaceToken;
  pl?: SpaceToken;
  m?: SpaceToken;
  mx?: SpaceToken;
  my?: SpaceToken;
  mt?: SpaceToken;
  mr?: SpaceToken;
  mb?: SpaceToken;
  ml?: SpaceToken;
  gap?: SpaceToken;
  /** Sizing */
  width?: string | number;
  height?: string | number;
  minWidth?: string | number;
  maxWidth?: string | number;
  minHeight?: string | number;
  maxHeight?: string | number;
  /** Visual — token only */
  bg?: ColorToken;
  color?: ColorToken;
  border?: `${string} ${string} ${ColorToken}`;
  borderColor?: ColorToken;
  radius?: RadiusToken;
  elevation?: ElevationToken;
  /** Flex/Grid */
  flex?: string | number;
  flexDirection?: 'row' | 'col' | 'row-reverse' | 'col-reverse';
  alignItems?: 'stretch' | 'center' | 'start' | 'end' | 'baseline';
  justifyContent?: 'stretch' | 'center' | 'start' | 'end' | 'between' | 'around' | 'evenly';
  gridTemplateColumns?: string;
  gridTemplateRows?: string;
}

export const Box = forwardRef<HTMLDivElement, BoxProps>(
  ({ as: Component = 'div', asChild, children, className, style, ...props }, ref) => {
    const Comp = asChild ? Slot : Component;
    // Transform token props to CSS variables / classNames
    const { className: computedClassName, style: computedStyle } = transformProps(props);
    return <Comp ref={ref} className={cn(computedClassName, className)} style={computedStyle}>{children}</Comp>;
  }
);
Box.displayName = 'Box';
```

### Text Primitive
```tsx
// primitives/Text.tsx
export interface TextProps extends Omit<HTMLAttributes<HTMLSpanElement>, 'as' | 'color'> {
  as?: ElementType;
  asChild?: boolean;
  /** Typography — token only */
  size?: keyof typeof textStyle;        // 'heading.xl' | 'body.base' | 'label.sm' | 'code.base'
  weight?: keyof typeof fontWeight;     // 'normal' | 'medium' | 'semibold' | 'bold'
  color?: ColorToken;
  align?: 'left' | 'center' | 'right' | 'justify';
  truncate?: boolean;
  noWrap?: boolean;
}

export const Text = forwardRef<HTMLSpanElement, TextProps>(
  ({ as: Component = 'p', asChild, size = 'body.base', weight, color, align, truncate, noWrap, children, className, style, ...props }, ref) => {
    const Comp = asChild ? Slot : Component;
    const textStyles = textStyle[size];
    return (
      <Comp
        ref={ref}
        className={cn(
          textStyles.fontFamily && `font-[${textStyles.fontFamily}]`,
          `text-${textStyles.size}`,
          `leading-${textStyles.lineHeight}`,
          weight && `font-${weight}`,
          color && `text-${color}`,
          align && `text-${align}`,
          truncate && 'truncate',
          noWrap && 'whitespace-nowrap',
          className
        )}
        style={{ ...textStyles, ...style }}
        {...props}
      >
        {children}
      </Comp>
    );
  }
);
Text.displayName = 'Text';
```

---

## Polymorphic `as` / `asChild` Pattern

### Slot Component (Radix-style)
```tsx
// primitives/Slot.tsx
import { type SlotProps, Slot as RadixSlot } from '@radix-ui/react-slot';

export interface PolymorphicProps<E extends ElementType = 'div'> extends SlotProps {
  as?: E;
  asChild?: boolean;
}

export function Slot<E extends ElementType = 'div'>({ asChild, children, ...props }: PolymorphicProps<E>) {
  return <RadixSlot asChild={asChild}>{children}</RadixSlot>;
}
```

### Usage
```tsx
// Button as link
<Button asChild>
  <Link href="/settings"><span>Settings</span></Link>
</Button>

// Box as semantic element
<Box as="section" aria-labelledby="heading">Content</Box>

// Text as heading
<Text as="h2" size="heading.md">Section Title</Text>
```

---

## Variant Discrimination — Semantic, Not Visual

### Button Variants
```tsx
// components/Button.tsx
type ButtonTone = 'primary' | 'secondary' | 'danger' | 'ghost' | 'link';
type ButtonSize = 'sm' | 'md' | 'lg';
type ButtonElement = 'button' | 'a' | 'input' | 'submit' | 'reset';

interface BaseButtonProps {
  tone?: ButtonTone;
  size?: ButtonSize;
  isLoading?: boolean;
  isDisabled?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
  fullWidth?: boolean;
}

// Discriminated union for element-specific props
type ButtonProps =
  | (BaseButtonProps & { as?: 'button'; type?: 'button' | 'submit' | 'reset'; onClick?: (e: React.MouseEvent<HTMLButtonElement>) => void; })
  | (BaseButtonProps & { as: 'a'; href: string; onClick?: (e: React.MouseEvent<HTMLAnchorElement>) => void; })
  | (BaseButtonProps & { as: 'input'; type: 'button' | 'submit' | 'reset' | 'image'; onClick?: (e: React.MouseEvent<HTMLInputElement>) => void; });

export const Button = forwardRef<HTMLButtonElement | HTMLAnchorElement | HTMLInputElement, ButtonProps>(
  ({ as = 'button', tone = 'primary', size = 'md', isLoading, isDisabled, leftIcon, rightIcon, fullWidth, children, className, ...props }, ref) => {
    const Comp = as;
    const sharedProps = {
      ref,
      className: cn(buttonStyles({ tone, size, fullWidth }), className),
      disabled: isDisabled || isLoading,
      'aria-busy': isLoading,
      'aria-disabled': isDisabled,
      ...props,
    };

    if (isLoading) {
      return (
        <Comp {...sharedProps}>
          <Spinner size={size} aria-hidden="true" />
          <span className="sr-only">Loading</span>
        </Comp>
      );
    }

    return (
      <Comp {...sharedProps}>
        {leftIcon && <span aria-hidden="true">{leftIcon}</span>}
        {children}
        {rightIcon && <span aria-hidden="true">{rightIcon}</span>}
      </Comp>
    );
  }
);
Button.displayName = 'Button';
```

### Card Variants
```tsx
// components/Card.tsx
type CardTone = 'default' | 'muted' | 'bordered' | 'elevated';
type CardPadding = 'none' | 'sm' | 'md' | 'lg';

interface CardProps {
  tone?: CardTone;
  padding?: CardPadding;
  elevation?: ElevationToken;
  hoverable?: boolean;
  children: React.ReactNode;
  as?: ElementType;
}

export const Card = forwardRef<HTMLDivElement, CardProps>(
  ({ tone = 'default', padding = 'md', elevation = 1, hoverable, children, as: Component = 'article', className, ...props }, ref) => {
    return (
      <Component
        ref={ref}
        className={cn(
          'rounded-lg transition-shadow',
          cardToneStyles[tone],
          cardPaddingStyles[padding],
          elevationStyles[elevation],
          hoverable && 'hover:shadow-elevation-3',
          className
        )}
        {...props}
      >
        {children}
      </Component>
    );
  }
);
Card.displayName = 'Card';
```

---

## Compound Components — Shared State

### Select
```tsx
// components/Select.tsx
import { createContext, useContext, useId, useState, type ReactNode } from 'react';

interface SelectContextValue {
  value: string;
  onValueChange: (value: string) => void;
  open: boolean;
  onOpenChange: (open: boolean) => void;
  triggerId: string;
  contentId: string;
}

const SelectContext = createContext<SelectContextValue | null>(null);

function useSelectContext() {
  const ctx = useContext(SelectContext);
  if (!ctx) throw new Error('Select components must be used within Select.Root');
  return ctx;
}

interface SelectRootProps {
  value: string;
  onValueChange: (value: string) => void;
  children: ReactNode;
}

export function SelectRoot({ value, onValueChange, children }: SelectRootProps) {
  const [open, setOpen] = useState(false);
  const triggerId = useId();
  const contentId = useId();

  return (
    <SelectContext.Provider value={{ value, onValueChange, open, onOpenChange: setOpen, triggerId, contentId }}>
      <div className="relative inline-block">{children}</div>
    </SelectContext.Provider>
  );
}

interface SelectTriggerProps extends Omit<ButtonProps, 'onClick' | 'children'> {
  placeholder?: string;
  children?: ReactNode;
}

export const SelectTrigger = forwardRef<HTMLButtonElement, SelectTriggerProps>(
  ({ placeholder, children, className, ...props }, ref) => {
    const { value, onValueChange, open, onOpenChange, triggerId, contentId } = useSelectContext();
    const displayValue = children || placeholder || value;

    return (
      <Button
        ref={ref}
        id={triggerId}
        aria-haspopup="listbox"
        aria-expanded={open}
        aria-controls={contentId}
        tone="secondary"
        justify="between"
        className={cn('w-full', className)}
        onClick={() => onOpenChange(!open)}
        {...props}
      >
        <span>{displayValue}</span>
        <ChevronDownIcon className={cn('transition-transform', open && 'rotate-180')} aria-hidden="true" />
      </Button>
    );
  }
);
SelectTrigger.displayName = 'SelectTrigger';

export const SelectContent = ({ children, className, ...props }: { children: ReactNode; className?: string } & Omit<HTMLDivElementProps, 'children'>) => {
  const { open, onOpenChange, contentId } = useSelectContext();
  if (!open) return null;

  return (
    <Portal>
      <div
        id={contentId}
        role="listbox"
        className={cn('absolute z-50 min-w-full rounded-md border bg-white shadow-lg', className)}
        {...props}
      >
        {children}
      </div>
    </Portal>
  );
};

export const SelectItem = forwardRef<HTMLDivElement, { value: string; disabled?: boolean; children: ReactNode }>(
  ({ value, disabled, children, className, ...props }, ref) => {
    const { value: currentValue, onValueChange, onOpenChange, contentId } = useSelectContext();
    const isSelected = value === currentValue;

    return (
      <div
        ref={ref}
        role="option"
        id={`${contentId}-${value}`}
        aria-selected={isSelected}
        aria-disabled={disabled}
        className={cn(
          'px-3 py-2 cursor-pointer',
          isSelected && 'bg-primary text-primary-fg',
          disabled && 'opacity-50 pointer-events-none',
          className
        )}
        onClick={() => { if (!disabled) { onValueChange(value); onOpenChange(false); } }}
        onKeyDown={(e) => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); if (!disabled) { onValueChange(value); onOpenChange(false); } } }}
        {...props}
      >
        {children}
      </div>
    );
  }
);
SelectItem.displayName = 'SelectItem';

// Export compound
export const Select = Object.assign(SelectRoot, {
  Trigger: SelectTrigger,
  Content: SelectContent,
  Item: SelectItem,
});
```

### Usage
```tsx
<Select value={country} onValueChange={setCountry}>
  <Select.Trigger placeholder="Select country" />
  <Select.Content>
    <Select.Item value="br">Brazil</Select.Item>
    <Select.Item value="us">United States</Select.Item>
    <Select.Item value="ar">Argentina</Select.Item>
  </Select.Content>
</Select>
```

---

## Generic Components — Type-Safe Data

### Table
```tsx
// components/Table.tsx
interface Column<T> {
  key: string;
  header: string;
  render: (row: T) => ReactNode;
  align?: 'left' | 'center' | 'right';
  width?: string;
}

interface TableProps<T> {
  data: T[];
  columns: Column<T>[];
  keyExtractor: (row: T) => string;
  onRowClick?: (row: T) => void;
  emptyMessage?: string;
  isLoading?: boolean;
}

export function Table<T>({ data, columns, keyExtractor, onRowClick, emptyMessage = 'No data', isLoading }: TableProps<T>) {
  if (isLoading) return <TableSkeleton columns={columns.length} />;
  if (data.length === 0) return <Text tone="muted" align="center" className="py-8">{emptyMessage}</Text>;

  return (
    <div className="overflow-x-auto">
      <table className="w-full border-collapse">
        <thead>
          <tr className="border-b border-border">
            {columns.map(col => (
              <th key={col.key} className={cn('px-4 py-3 text-left text-sm font-medium text-fg-muted', col.align && `text-${col.align}`, col.width && `w-[${col.width}]`)} style={{ width: col.width }}>
                {col.header}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {data.map(row => (
            <tr key={keyExtractor(row)} className={cn('border-b border-border/50', onRowClick && 'cursor-pointer hover:bg-bg-muted')}>
              {columns.map(col => (
                <td key={col.key} className={cn('px-4 py-3 text-sm', col.align && `text-${col.align}`)}>
                  {col.render(row)}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

```tsx
// Usage — fully typed
interface User { id: string; name: string; email: string; role: 'admin' | 'member'; status: 'active' | 'pending'; }

const columns: Column<User>[] = [
  { key: 'name', header: 'Name', render: row => row.name },
  { key: 'email', header: 'Email', render: row => row.email },
  { key: 'role', header: 'Role', render: row => <Badge tone={row.role === 'admin' ? 'primary' : 'secondary'}>{row.role}</Badge> },
  { key: 'status', header: 'Status', render: row => <StatusIndicator status={row.status} /> },
];

<Table<User>
  data={users}
  columns={columns}
  keyExtractor={u => u.id}
  onRowClick={handleRowClick}
/>
```

---

## Form Components — Controlled + Uncontrolled

### Input
```tsx
// components/Input.tsx
interface InputProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'size'> {
  label: string;
  error?: string;
  hint?: string;
  size?: 'sm' | 'md' | 'lg';
  leftElement?: React.ReactNode;
  rightElement?: React.ReactNode;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, hint, size = 'md', leftElement, rightElement, className, id, 'aria-describedby': ariaDescribedBy, ...props }, ref) => {
    const inputId = useId();
    const describedBy = [error && `${inputId}-error`, hint && `${inputId}-hint`, ariaDescribedBy].filter(Boolean).join(' ') || undefined;

    return (
      <div className="w-full">
        <label htmlFor={id || inputId} className="block text-sm font-medium text-fg mb-1.5">{label}</label>
        <div className="relative">
          {leftElement && <span className="absolute inset-y-0 left-0 flex items-center pl-3 text-fg-muted pointer-events-none">{leftElement}</span>}
          <input
            ref={ref}
            id={id || inputId}
            className={cn(
              'w-full rounded-md border bg-bg text-fg placeholder-fg-muted transition-colors',
              'focus:outline-none focus:ring-2 focus:ring-focus focus:border-transparent',
              'disabled:bg-bg-muted disabled:cursor-not-allowed',
              'aria-invalid:border-danger aria-invalid:focus:ring-danger',
              leftElement && 'pl-10',
              rightElement && 'pr-10',
              size === 'sm' && 'px-3 py-1.5 text-sm',
              size === 'md' && 'px-4 py-2 text-sm',
              size === 'lg' && 'px-4 py-3 text-base',
              error && 'border-danger',
              className
            )}
            aria-invalid={!!error}
            aria-describedby={describedBy}
            {...props}
          />
          {rightElement && <span className="absolute inset-y-0 right-0 flex items-center pr-3 text-fg-muted pointer-events-none">{rightElement}</span>}
        </div>
        {error && <p id={`${inputId}-error`} role="alert" className="mt-1.5 text-sm text-danger">{error}</p>}
        {hint && !error && <p id={`${inputId}-hint`} className="mt-1.5 text-sm text-fg-muted">{hint}</p>}
      </div>
    );
  }
);
Input.displayName = 'Input';
```

---

## Testing Types

### Component Prop Tests
```tsx
// Button.test-d.tsx — type-only tests (no runtime)
import { expectTypeOf } from 'vitest';
import type { ButtonProps } from './Button';

test('Button props are correctly typed', () => {
  // Valid
  expectTypeOf<ButtonProps>().toEqualTypeOf<{ tone?: 'primary' | 'secondary' | 'danger' | 'ghost' | 'link' }>();
  expectTypeOf<ButtonProps>().toEqualTypeOf<{ size?: 'sm' | 'md' | 'lg' }>();
  expectTypeOf<ButtonProps>().toEqualTypeOf<{ asChild?: boolean }>();

  // Polymorphic — anchor requires href
  type AnchorProps = ButtonProps & { as: 'a'; href: string };
  expectTypeOf<AnchorProps>().toHaveProperty('href').toBeString();

  // Button element — no href
  type ButtonElementProps = ButtonProps & { as?: 'button' };
  expectTypeOf<ButtonElementProps>().not.toHaveProperty('href');
});
```

### Variant Exhaustiveness
```tsx
// utils/exhaustive.ts
export function assertNever(x: never): never {
  throw new Error(`Unexpected value: ${x}`);
}

// In component
function getToneStyles(tone: ButtonTone) {
  switch (tone) {
    case 'primary': return primaryStyles;
    case 'secondary': return secondaryStyles;
    case 'danger': return dangerStyles;
    case 'ghost': return ghostStyles;
    case 'link': return linkStyles;
    default: return assertNever(tone); // compile error if new variant added
  }
}
```

---

## Anti-Patterns

| Anti-Pattern | Why | Fix |
|--------------|-----|-----|
| `interface Props { className?: string; style?: CSSProperties; }` | Escape hatch breaks design system | Token props only |
| `type Props = { [key: string]: any; }` | No type safety | Strict interfaces |
| `ComponentPropsWithoutRef<'button'>` | Imports all native props including `onDrag`, `onCopy` | Explicit `Omit<HTMLButtonElement, 'children'>` |
| `as?: keyof JSX.IntrinsicElements` | Allows invalid elements | `ElementType` + `Slot` |
| No `forwardRef` on primitives | Breaks composition, focus, measurement | Always `forwardRef` |
| `any` in render props | Loses type inference | Generics `<T>` |
| Optional `onClick` on `<Button as="a">` | Anchor needs `href`, not `onClick` | Discriminated union |
| String literal unions without exhaustiveness | New variant = silent bug | `assertNever` in switch |