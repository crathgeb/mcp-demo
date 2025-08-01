# Creating a React Component

This prompt will help you create a new React component following modern best practices.

## Basic Component Structure

1. Create a new file with a `.tsx` extension (for TypeScript) or `.jsx` (for JavaScript)
2. Name the file using PascalCase (e.g., `Button.tsx`, `UserProfile.tsx`)
3. Use this basic template:

```tsx
// Import React if needed (not required in newer React versions)
import React from 'react';

// Define the props interface (if using TypeScript)
interface Props {
  // Define your props here
  label?: string;
  onClick?: () => void;
}

// Create the component
export const MyComponent: React.FC<Props> = ({ label, onClick }) => {
  return (
    <div>
      {/* Your JSX goes here */}
    </div>
  );
};

// For JavaScript, use this simpler version:
/*
export const MyComponent = ({ label, onClick }) => {
  return (
    <div>
      {/* Your JSX goes here */}
    </div>
  );
};
*/
```

## Best Practices

1. **One Component Per File**: Keep each component in its own file
2. **Clear Naming**: Use descriptive, PascalCase names for components
3. **Props Interface**: Define clear prop types using TypeScript interfaces
4. **Default Exports**: Prefer named exports over default exports
5. **Folder Structure**: Place related components in a shared folder

## Example Component

Here's a complete example of a button component:

```tsx
import React from 'react';

interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  variant = 'primary',
  disabled = false,
}) => {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`button ${variant}`}
    >
      {label}
    </button>
  );
};
```

## Component Organization

Suggested folder structure:
```
src/
  components/
    Button/
      Button.tsx
      Button.test.tsx
      Button.css
    UserProfile/
      UserProfile.tsx
      UserProfile.test.tsx
      UserProfile.css
```

Remember to:
- Keep components focused and single-purpose
- Use TypeScript for better type safety
- Include proper prop validation
- Add comments for complex logic
- Consider component reusability
