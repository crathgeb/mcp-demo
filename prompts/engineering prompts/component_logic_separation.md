# Refactoring React Components: Separating Logic from Presentation

This prompt will guide you through the process of refactoring a React component to separate business logic from presentation logic, following the Container/Presenter pattern.

## Identifying When to Separate

Look for these signs that a component needs separation:
1. Component handles both data fetching/manipulation AND rendering
2. Contains multiple useEffect hooks or complex state management
3. Has both business logic and complex UI rendering
4. Mixes API calls with presentation code
5. Component is difficult to test due to mixed responsibilities

## Step-by-Step Refactoring Process

### 1. Start with Original Component
```tsx
// UserProfile.tsx (Original)
export const UserProfile = () => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUserData()
      .then(data => setUser(data))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  const handleUpdateUser = async (updates: Partial<User>) => {
    try {
      const updatedUser = await updateUserAPI(updates);
      setUser(updatedUser);
    } catch (err) {
      setError(err.message);
    }
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="user-profile">
      <h2>{user?.name}</h2>
      <div className="user-details">
        <p>{user?.email}</p>
        <button onClick={() => handleUpdateUser({ status: 'active' })}>
          Activate
        </button>
      </div>
    </div>
  );
};
```

### 2. Create Presenter Component
```tsx
// UserProfilePresenter.tsx
interface UserProfilePresenterProps {
  user: User | null;
  loading: boolean;
  error: string | null;
  onUpdateUser: (updates: Partial<User>) => void;
}

export const UserProfilePresenter: React.FC<UserProfilePresenterProps> = ({
  user,
  loading,
  error,
  onUpdateUser,
}) => {
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="user-profile">
      <h2>{user?.name}</h2>
      <div className="user-details">
        <p>{user?.email}</p>
        <button onClick={() => onUpdateUser({ status: 'active' })}>
          Activate
        </button>
      </div>
    </div>
  );
};
```

### 3. Create Container Component
```tsx
// UserProfileContainer.tsx
export const UserProfileContainer = () => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUserData()
      .then(data => setUser(data))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  const handleUpdateUser = async (updates: Partial<User>) => {
    try {
      const updatedUser = await updateUserAPI(updates);
      setUser(updatedUser);
    } catch (err) {
      setError(err.message);
    }
  };

  return (
    <UserProfilePresenter
      user={user}
      loading={loading}
      error={error}
      onUpdateUser={handleUpdateUser}
    />
  );
};
```

## Best Practices for Separation

### Container (Smart) Component Responsibilities:
- Data fetching and state management
- Business logic and data transformation
- Error handling
- Event handlers and callbacks
- Integration with global state (Redux, Context, etc.)

### Presenter (Dumb) Component Responsibilities:
- Rendering UI elements
- Styling and layout
- Handling local UI state only
- Receiving and using props
- No direct data fetching or business logic

## Benefits of Separation

1. **Improved Testability**
   - Presenter components are easy to test with mock props
   - Container logic can be tested independently of UI

2. **Better Reusability**
   - Presenters can be reused with different data sources
   - Containers can be adapted for different UI presentations

3. **Easier Maintenance**
   - Clear separation of concerns
   - Changes to business logic don't affect presentation
   - UI changes don't risk breaking business logic

## When to Use Custom Hooks

Consider extracting logic to custom hooks when:
```tsx
// userProfile.hook.ts
export const useUserProfile = () => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUserData()
      .then(data => setUser(data))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  const handleUpdateUser = async (updates: Partial<User>) => {
    try {
      const updatedUser = await updateUserAPI(updates);
      setUser(updatedUser);
    } catch (err) {
      setError(err.message);
    }
  };

  return {
    user,
    loading,
    error,
    handleUpdateUser,
  };
};
```

Then your container becomes even simpler:
```tsx
export const UserProfileContainer = () => {
  const { user, loading, error, handleUpdateUser } = useUserProfile();

  return (
    <UserProfilePresenter
      user={user}
      loading={loading}
      error={error}
      onUpdateUser={handleUpdateUser}
    />
  );
};
```

## Review Checklist

When refactoring, ensure:
- ✓ Props interface is well-defined
- ✓ Container handles all data and logic
- ✓ Presenter is pure and predictable
- ✓ No business logic in presenter
- ✓ No API calls in presenter
- ✓ Props names are clear and consistent
- ✓ Error handling is comprehensive
- ✓ Loading states are managed
- ✓ Component responsibilities are clear
- ✓ Tests can be written independently