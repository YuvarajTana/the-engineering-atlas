# LinkedIn Clone: Design Patterns, Clean Architecture, and SOLID Principles

Building a large-scale front-end application like a LinkedIn clone using **Next.js 14**, **Material-UI (MUI) v5**, and **TypeScript** requires meticulous planning and adherence to solid software engineering principles. This document outlines the implementation of **Design Patterns**, **Clean Architecture**, and **SOLID Principles** in the project, complete with detailed explanations and code samples.

---

## Table of Contents

1. [Design Patterns](#1-design-patterns)
   - [1.1 Component-Based Pattern](#11-component-based-pattern)
   - [1.2 Container and Presentational Components (Smart and Dumb Components)](#12-container-and-presentational-components-smart-and-dumb-components)
   - [1.3 Observer Pattern with State Management (Using Redux)](#13-observer-pattern-with-state-management-using-redux)
   - [1.4 Factory Pattern for Dynamic Component Rendering](#14-factory-pattern-for-dynamic-component-rendering)
2. [Clean Architecture](#2-clean-architecture)
   - [2.1 Layered Structure](#21-layered-structure)
   - [2.2 Domain Layer (Entities)](#22-domain-layer-entities)
   - [2.3 Application Layer (Use Cases)](#23-application-layer-use-cases)
   - [2.4 Interface Adapters (Repositories and APIs)](#24-interface-adapters-repositories-and-apis)
   - [2.5 Infrastructure Layer](#25-infrastructure-layer)
   - [2.6 Presentation Layer (Containers and Components)](#26-presentation-layer-containers-and-components)
3. [SOLID Principles](#3-solid-principles)
   - [3.1 Single Responsibility Principle (SRP)](#31-single-responsibility-principle-srp)
   - [3.2 Open/Closed Principle (OCP)](#32-openclosed-principle-ocp)
   - [3.3 Liskov Substitution Principle (LSP)](#33-liskov-substitution-principle-lsp)
   - [3.4 Interface Segregation Principle (ISP)](#34-interface-segregation-principle-isp)
   - [3.5 Dependency Inversion Principle (DIP)](#35-dependency-inversion-principle-dip)
4. [Complete Example: Integrating Design Patterns, Clean Architecture, and SOLID Principles](#4-complete-example-integrating-design-patterns-clean-architecture-and-solid-principles)
   - [4.1 Define the Domain Entity](#41-define-the-domain-entity)
   - [4.2 Define the Repository Interface](#42-define-the-repository-interface)
   - [4.3 Implement the Repository](#43-implement-the-repository)
   - [4.4 Define Use Cases](#44-define-use-cases)
   - [4.5 Setup Infrastructure](#45-setup-infrastructure)
   - [4.6 Setup Redux Slice](#46-setup-redux-slice)
   - [4.7 Create Presentation Components](#47-create-presentation-components)
   - [4.8 Create Containers](#48-create-containers)
   - [4.9 Utilize Dependency Injection in Pages](#49-utilize-dependency-injection-in-pages)
5. [Conclusion](#5-conclusion)

---

## 1. Design Patterns

**Design Patterns** are reusable solutions to common problems in software design. They provide a template for structuring your code in a way that is maintainable, scalable, and efficient. In the context of a LinkedIn clone built with Next.js, MUI, and TypeScript, several design patterns can be particularly beneficial.

### 1.1 Component-Based Pattern

Next.js and React promote a component-based architecture. Breaking the UI into reusable components enhances maintainability and reusability.

**Example: Creating a Reusable `Button` Component with MUI**

```tsx
// components/ui/Button.tsx
import React from 'react';
import { Button as MUIButton, ButtonProps as MUIButtonProps } from '@mui/material';
import { styled } from '@mui/material/styles';

const StyledButton = styled(MUIButton)(({ theme }) => ({
  borderRadius: theme.shape.borderRadius,
  padding: theme.spacing(1, 3),
}));

interface ButtonProps extends MUIButtonProps {
  variant?: 'contained' | 'outlined' | 'text';
}

const Button: React.FC<ButtonProps> = ({ children, ...props }) => {
  return <StyledButton {...props}>{children}</StyledButton>;
};

export default Button;
```


Usage in a Page:

```tsx
// pages/index.tsx
import React from 'react';
import Button from '../components/ui/Button';
import { Container, Typography } from '@mui/material';

const HomePage: React.FC = () => {
  return (
    <Container>
      <Typography variant="h4" gutterBottom>
        Welcome to LinkedIn Clone
      </Typography>
      <Button variant="contained" color="primary">
        Get Started
      </Button>
    </Container>
  );
};

export default HomePage;

```

### 1.2 Container and Presentational Components (Smart and Dumb Components)

This pattern separates components that handle logic (containers) from those that handle presentation (presentational).

Example: Creating a Profile Container and Profile Component

```tsx
// containers/ProfileContainer.tsx
import React, { useEffect, useState } from 'react';
import Profile from '../components/Profile';
import { fetchUserProfile } from '../services/userService';
import { UserProfile } from '../types';

const ProfileContainer: React.FC = () => {
  const [profile, setProfile] = useState<UserProfile | null>(null);
  const [loading, setLoading] = useState<boolean>(true);

  useEffect(() => {
    const getProfile = async () => {
      const data = await fetchUserProfile();
      setProfile(data);
      setLoading(false);
    };
    getProfile();
  }, []);

  if (loading) return <div>Loading...</div>;

  return <Profile user={profile!} />;
};

export default ProfileContainer;
```

```tsx
// components/Profile.tsx
import React from 'react';
import { UserProfile } from '../types';
import { Card, CardContent, Typography, Avatar } from '@mui/material';

interface ProfileProps {
  user: UserProfile;
}

const Profile: React.FC<ProfileProps> = ({ user }) => {
  return (
    <Card>
      <CardContent>
        <Avatar src={user.avatarUrl} alt={user.name} />
        <Typography variant="h5">{user.name}</Typography>
        <Typography variant="body2">{user.headline}</Typography>
      </CardContent>
    </Card>
  );
};

export default Profile;
```
### 1.3 Observer Pattern with State Management (Using Redux)

The Observer Pattern allows components to subscribe to state changes and react accordingly. Redux is a popular state management library that embodies this pattern.

Example: Setting Up Redux with Next.js and TypeScript

    Install Dependencies

npm install @reduxjs/toolkit react-redux

    Configure the Store

```tsx
// store/index.ts
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';

export const store = configureStore({
  reducer: {
    user: userReducer,
    // Add other reducers here
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

    Create a User Slice

```tsx
// store/userSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import { UserProfile } from '../types';

interface UserState {
  profile: UserProfile | null;
}

const initialState: UserState = {
  profile: null,
};

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    setProfile(state, action: PayloadAction<UserProfile>) {
      state.profile = action.payload;
    },
    clearProfile(state) {
      state.profile = null;
    },
  },
});

export const { setProfile, clearProfile } = userSlice.actions;
export default userSlice.reducer;
```

    Provide the Store to the App

```tsx
// pages/_app.tsx
import React from 'react';
import { AppProps } from 'next/app';
import { Provider } from 'react-redux';
import { store } from '../store';
import { ThemeProvider, createTheme } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';

const theme = createTheme({
  // Customize your theme here
});

const MyApp: React.FC<AppProps> = ({ Component, pageProps }) => {
  return (
    <Provider store={store}>
      <ThemeProvider theme={theme}>
        <CssBaseline />
        <Component {...pageProps} />
      </ThemeProvider>
    </Provider>
  );
};

export default MyApp;
```

    Connecting Components to the Store

```tsx
// containers/ProfileContainer.tsx
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import Profile from '../components/Profile';
import { fetchUserProfile } from '../services/userService';
import { setProfile } from '../store/userSlice';
import { RootState } from '../store';

const ProfileContainer: React.FC = () => {
  const dispatch = useDispatch();
  const profile = useSelector((state: RootState) => state.user.profile);
  const [loading, setLoading] = React.useState<boolean>(true);

  useEffect(() => {
    const getProfile = async () => {
      const data = await fetchUserProfile();
      dispatch(setProfile(data));
      setLoading(false);
    };
    getProfile();
  }, [dispatch]);

  if (loading) return <div>Loading...</div>;

  return profile ? <Profile user={profile} /> : <div>No Profile Found</div>;
};

export default ProfileContainer;
```

### 1.4 Factory Pattern for Dynamic Component Rendering

The Factory Pattern allows for the creation of objects without specifying the exact class. In React, this can be used to render different components based on certain conditions.

Example: Dynamic Rendering of Feed Items

```tsx
// components/feed/FeedItemFactory.tsx
import React from 'react';
import { FeedItemType } from '../../types';
import TextPost from './TextPost';
import ImagePost from './ImagePost';
import VideoPost from './VideoPost';

interface FeedItemFactoryProps {
  item: FeedItemType;
}

const FeedItemFactory: React.FC<FeedItemFactoryProps> = ({ item }) => {
  switch (item.type) {
    case 'text':
      return <TextPost content={item.content} author={item.author} />;
    case 'image':
      return <ImagePost imageUrl={item.imageUrl} author={item.author} />;
    case 'video':
      return <VideoPost videoUrl={item.videoUrl} author={item.author} />;
    default:
      return null;
  }
};

export default FeedItemFactory;
```

Usage in a Feed Component:

```tsx
// components/Feed.tsx
import React from 'react';
import FeedItemFactory from './feed/FeedItemFactory';
import { FeedItemType } from '../types';
import { List } from '@mui/material';

interface FeedProps {
  items: FeedItemType[];
}

const Feed: React.FC<FeedProps> = ({ items }) => {
  return (
    <List>
      {items.map((item) => (
        <FeedItemFactory key={item.id} item={item} />
      ))}
    </List>
  );
};

export default Feed;
```

## 2. Clean Architecture

Clean Architecture is a design philosophy that emphasizes separation of concerns, making the system independent of frameworks, UI, databases, and other external factors. This architecture ensures that the business logic is at the core, free from dependencies on other layers.

### 2.1 Layered Structure

A typical Clean Architecture for a Next.js project can be organized into the following layers:

    Entities (Domain Layer): Business models and logic.
    Use Cases (Application Layer): Application-specific business rules.
    Interface Adapters (Presentation Layer): Controllers, presenters, and view models.
    Frameworks & Drivers (Infrastructure Layer): External systems like APIs, databases, UI frameworks.

Project Structure Example:

/src
  /components
    /ui
    /Feed
    /Profile
  /containers
  /domains
    /User
      userEntity.ts
      userService.ts
      userRepository.ts
  /useCases
    getUserProfile.ts
    updateUserProfile.ts
  /interfaces
    /api
      userApi.ts
  /infrastructure
    /api
    /database
  /pages
  /store
  /types

### 2.2 Domain Layer (Entities)

Defines the core business models.
```tsx
// src/domains/User/userEntity.ts
export interface UserProfile {
  id: string;
  name: string;
  avatarUrl: string;
  headline: string;
  // Add other relevant fields
}
```
### 2.3 Application Layer (Use Cases)

Contains application-specific business logic.
```tsx
// src/useCases/getUserProfile.ts
import { UserProfile } from '../domains/User/userEntity';
import { UserRepository } from '../domains/User/userRepository';

export class GetUserProfile {
  constructor(private userRepository: UserRepository) {}

  async execute(userId: string): Promise<UserProfile> {
    // Business logic can be added here
    return this.userRepository.getUserById(userId);
  }
}
```
### 2.4 Interface Adapters (Repositories and APIs)

Implements interfaces defined in the domain layer.
```tsx
// src/domains/User/userRepository.ts
import { UserProfile } from './userEntity';

export interface UserRepository {
  getUserById(userId: string): Promise<UserProfile>;
}

// src/interfaces/api/userApi.ts
import axios from 'axios';
import { UserRepository } from '../../domains/User/userRepository';
import { UserProfile } from '../../domains/User/userEntity';

export class UserApi implements UserRepository {
  private apiClient = axios.create({
    baseURL: '/api',
  });

  async getUserById(userId: string): Promise<UserProfile> {
    const response = await this.apiClient.get<UserProfile>(`/users/${userId}`);
    return response.data;
  }
}
```
### 2.5 Infrastructure Layer

Handles external systems and frameworks.

```tsx
// src/infrastructure/api/userService.ts
import { UserApi } from '../../interfaces/api/userApi';
import { GetUserProfile } from '../../useCases/getUserProfile';

const userApi = new UserApi();
const getUserProfile = new GetUserProfile(userApi);

export { getUserProfile };
```

### 2.6 Presentation Layer (Containers and Components)

Connects the use cases with the UI components.

```tsx
// src/containers/ProfileContainer.tsx
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import Profile from '../components/Profile';
import { getUserProfile } from '../infrastructure/api/userService';
import { setProfile } from '../store/userSlice';
import { RootState } from '../store';

interface ProfileContainerProps {
  userId: string;
}

const ProfileContainer: React.FC<ProfileContainerProps> = ({ userId }) => {
  const dispatch = useDispatch();
  const profile = useSelector((state: RootState) => state.user.profile);
  const [loading, setLoading] = React.useState<boolean>(true);

  useEffect(() => {
    const fetchProfile = async () => {
      const data = await getUserProfile.execute(userId);
      dispatch(setProfile(data));
      setLoading(false);
    };
    fetchProfile();
  }, [dispatch, userId]);

  if (loading) return <div>Loading...</div>;

  return profile ? <Profile user={profile} /> : <div>No Profile Found</div>;
};

export default ProfileContainer;
```

### 2.7 Benefits of Clean Architecture

    Maintainability: Clear separation makes the codebase easier to manage.
    Testability: Each layer can be tested in isolation.
    Flexibility: Easily swap out frameworks or external systems without affecting core logic.
    Scalability: Facilitates adding new features without significant rewrites.


## 3. SOLID Principles

SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable. Applying these principles to your LinkedIn clone will ensure a robust and scalable codebase.
### 3.1 Single Responsibility Principle (SRP)

Definition: A class or module should have one, and only one, reason to change.

Application:

    Components: Each component should handle a single functionality.
    Services: Each service should handle a specific aspect of the application (e.g., user service, post service).

Example: Separating Profile and EditProfile Components

```tsx
// components/Profile.tsx
import React from 'react';
import { UserProfile } from '../types';
import { Card, CardContent, Typography, Avatar } from '@mui/material';

interface ProfileProps {
  user: UserProfile;
}

const Profile: React.FC<ProfileProps> = ({ user }) => {
  return (
    <Card>
      <CardContent>
        <Avatar src={user.avatarUrl} alt={user.name} />
        <Typography variant="h5">{user.name}</Typography>
        <Typography variant="body2">{user.headline}</Typography>
      </CardContent>
    </Card>
  );
};

export default Profile;
```

```tsx
// components/EditProfile.tsx
import React, { useState } from 'react';
import { UserProfile } from '../types';
import { TextField, Button } from '@mui/material';

interface EditProfileProps {
  user: UserProfile;
  onSave: (updatedUser: UserProfile) => void;
}

const EditProfile: React.FC<EditProfileProps> = ({ user, onSave }) => {
  const [name, setName] = useState<string>(user.name);
  const [headline, setHeadline] = useState<string>(user.headline);

  const handleSubmit = () => {
    onSave({ ...user, name, headline });
  };

  return (
    <form>
      <TextField label="Name" value={name} onChange={(e) => setName(e.target.value)} />
      <TextField label="Headline" value={headline} onChange={(e) => setHeadline(e.target.value)} />
      <Button onClick={handleSubmit} variant="contained" color="primary">
        Save
      </Button>
    </form>
  );
};

export default EditProfile;
```

### 3.2 Open/Closed Principle (OCP)

Definition: Software entities should be open for extension but closed for modification.

Application:

    Extensible Components: Use props and composition to extend components without altering their core code.
    Higher-Order Components (HOCs): Enhance components with additional functionality.

Example: Creating a Themed Button
```tsx
// components/ui/ThemedButton.tsx
import React from 'react';
import Button from './Button';
import { ButtonProps } from '@mui/material';

interface ThemedButtonProps extends ButtonProps {
  variant?: 'primary' | 'secondary';
}

const ThemedButton: React.FC<ThemedButtonProps> = ({ variant = 'primary', ...props }) => {
  const color = variant === 'primary' ? 'primary' : 'secondary';
  return <Button color={color} {...props} />;
};

export default ThemedButton;
```

Usage:

```tsx
// components/SomeComponent.tsx
import React from 'react';
import ThemedButton from './ui/ThemedButton';

const SomeComponent: React.FC = () => {
  return (
    <div>
      <ThemedButton variant="primary">Primary Action</ThemedButton>
      <ThemedButton variant="secondary">Secondary Action</ThemedButton>
    </div>
  );
};

export default SomeComponent;
```

### 3.3 Liskov Substitution Principle (LSP)

Definition: Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.

Application:

    Component Inheritance: Ensure derived components adhere to the behavior of their base components.
    Type Safety: Utilize TypeScript to enforce correct typings and substitutions.

Example: Extending a Base Modal Component

```tsx
// components/ui/BaseModal.tsx
import React from 'react';
import { Modal, Box } from '@mui/material';

interface BaseModalProps {
  open: boolean;
  onClose: () => void;
  children: React.ReactNode;
}

const BaseModal: React.FC<BaseModalProps> = ({ open, onClose, children }) => {
  return (
    <Modal open={open} onClose={onClose}>
      <Box
        sx={{
          position: 'absolute' as const,
          top: '50%',
          left: '50%',
          transform: 'translate(-50%, -50%)',
          bgcolor: 'background.paper',
          boxShadow: 24,
          p: 4,
        }}
      >
        {children}
      </Box>
    </Modal>
  );
};

export default BaseModal;
```
```tsx
// components/ui/ConfirmModal.tsx
import React from 'react';
import BaseModal from './BaseModal';
import { Button, Typography } from '@mui/material';

interface ConfirmModalProps {
  open: boolean;
  onClose: () => void;
  onConfirm: () => void;
  message: string;
}

const ConfirmModal: React.FC<ConfirmModalProps> = ({ open, onClose, onConfirm, message }) => {
  return (
    <BaseModal open={open} onClose={onClose}>
      <Typography variant="h6">{message}</Typography>
      <Button onClick={onConfirm} variant="contained" color="primary">
        Confirm
      </Button>
      <Button onClick={onClose} variant="outlined" color="secondary">
        Cancel
      </Button>
    </BaseModal>
  );
};

export default ConfirmModal;
```

Usage:

```tsx
// components/DeletePostButton.tsx
import React, { useState } from 'react';
import { IconButton } from '@mui/material';
import DeleteIcon from '@mui/icons-material/Delete';
import ConfirmModal from './ui/ConfirmModal';
import { deletePost } from '../services/postService';

interface DeletePostButtonProps {
  postId: string;
}

const DeletePostButton: React.FC<DeletePostButtonProps> = ({ postId }) => {
  const [open, setOpen] = useState<boolean>(false);

  const handleDelete = async () => {
    await deletePost(postId);
    setOpen(false);
    // Additional logic like refreshing the feed
  };

  return (
    <>
      <IconButton onClick={() => setOpen(true)}>
        <DeleteIcon />
      </IconButton>
      <ConfirmModal
        open={open}
        onClose={() => setOpen(false)}
        onConfirm={handleDelete}
        message="Are you sure you want to delete this post?"
      />
    </>
  );
};

export default DeletePostButton;
```


### 3.4 Interface Segregation Principle (ISP)

Definition: Clients should not be forced to depend on interfaces they do not use.

Application:

    Focused Props: Design component APIs with only necessary props.
    Separate Interfaces: Split large interfaces into smaller, more specific ones.

Example: Splitting a Large UserProps Interface

```tsx
// types/index.ts
export interface BasicUserInfo {
  id: string;
  name: string;
  avatarUrl: string;
}

export interface DetailedUserInfo extends BasicUserInfo {
  headline: string;
  experience: string[];
  education: string[];
}

// components/ProfileSummary.tsx
import React from 'react';
import { BasicUserInfo } from '../types';
import { Card, CardContent, Typography, Avatar } from '@mui/material';

interface ProfileSummaryProps {
  user: BasicUserInfo;
}

const ProfileSummary: React.FC<ProfileSummaryProps> = ({ user }) => {
  return (
    <Card>
      <CardContent>
        <Avatar src={user.avatarUrl} alt={user.name} />
        <Typography variant="h6">{user.name}</Typography>
      </CardContent>
    </Card>
  );
};

export default ProfileSummary;
```

```tsx
// components/ProfileDetails.tsx
import React from 'react';
import { DetailedUserInfo } from '../types';
import { Card, CardContent, Typography, List, ListItem } from '@mui/material';

interface ProfileDetailsProps {
  user: DetailedUserInfo;
}

const ProfileDetails: React.FC<ProfileDetailsProps> = ({ user }) => {
  return (
    <Card>
      <CardContent>
        <Typography variant="h5">{user.name}</Typography>
        <Typography variant="body1">{user.headline}</Typography>
        <Typography variant="h6">Experience</Typography>
        <List>
          {user.experience.map((exp, index) => (
            <ListItem key={index}>{exp}</ListItem>
          ))}
        </List>
        <Typography variant="h6">Education</Typography>
        <List>
          {user.education.map((edu, index) => (
            <ListItem key={index}>{edu}</ListItem>
          ))}
        </List>
      </CardContent>
    </Card>
  );
};

export default ProfileDetails;
```

### 3.5 Dependency Inversion Principle (DIP)

Definition: High-level modules should not depend on low-level modules; both should depend on abstractions.

Application:

    Abstraction Layers: Depend on interfaces or abstract classes rather than concrete implementations.
    Dependency Injection: Inject dependencies into components or services.

Example: Injecting a User Service into a Use Case

```tsx
// domains/User/userRepository.ts
export interface UserRepository {
  getUserById(userId: string): Promise<UserProfile>;
  updateUser(user: UserProfile): Promise<void>;
}
```

```tsx
// useCases/updateUserProfile.ts
import { UserRepository } from '../domains/User/userRepository';
import { UserProfile } from '../domains/User/userEntity';

export class UpdateUserProfile {
  constructor(private userRepository: UserRepository) {}

  async execute(user: UserProfile): Promise<void> {
    // Business logic for updating user profile
    await this.userRepository.updateUser(user);
  }
}
```

```tsx
// infrastructure/api/userService.ts
import { UserApi } from '../../interfaces/api/userApi';
import { UpdateUserProfile } from '../../useCases/updateUserProfile';

const userApi = new UserApi();
const updateUserProfile = new UpdateUserProfile(userApi);

export { updateUserProfile };
```

```tsx
// containers/EditProfileContainer.tsx
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import EditProfile from '../components/EditProfile';
import { updateUserProfile } from '../infrastructure/api/userService';
import { setProfile } from '../store/userSlice';
import { RootState } from '../store';
import { UserProfile } from '../domains/User/userEntity';

interface EditProfileContainerProps {
  userId: string;
}

const EditProfileContainer: React.FC<EditProfileContainerProps> = ({ userId }) => {
  const dispatch = useDispatch();
  const profile = useSelector((state: RootState) => state.user.profile);

  const handleSave = async (updatedUser: UserProfile) => {
    await updateUserProfile.execute(updatedUser);
    dispatch(setProfile(updatedUser));
    // Additional logic like notifying the user
  };

  return profile ? <EditProfile user={profile} onSave={handleSave} /> : <div>Loading...</div>;
};

export default EditProfileContainer;
```

### 3.6 Benefits of SOLID Principles

    Maintainability: Easier to update and modify code without introducing bugs.
    Scalability: Facilitates adding new features without significant rewrites.
    Reusability: Well-designed components and modules can be reused across different parts of the application.
    Testability: Decoupled and single-responsibility modules are simpler to test.


## 4. Complete Example: Integrating Design Patterns, Clean Architecture, and SOLID Principles

Let's integrate these concepts into a cohesive example within our LinkedIn clone.
4.1 Define the Domain Entity

```tsx
// src/domains/User/userEntity.ts
export interface UserProfile {
  id: string;
  name: string;
  avatarUrl: string;
  headline: string;
  experience: string[];
  education: string[];
}
```

### 4.2 Define the Repository Interface

```tsx
// src/domains/User/userRepository.ts
import { UserProfile } from './userEntity';

export interface UserRepository {
  getUserById(userId: string): Promise<UserProfile>;
  updateUser(user: UserProfile): Promise<void>;
}
```

### 4.3 Implement the Repository

```tsx
// src/interfaces/api/userApi.ts
import axios from 'axios';
import { UserRepository } from '../../domains/User/userRepository';
import { UserProfile } from '../../domains/User/userEntity';

export class UserApi implements UserRepository {
  private apiClient = axios.create({
    baseURL: '/api',
  });

  async getUserById(userId: string): Promise<UserProfile> {
    const response = await this.apiClient.get<UserProfile>(`/users/${userId}`);
    return response.data;
  }

  async updateUser(user: UserProfile): Promise<void> {
    await this.apiClient.put(`/users/${user.id}`, user);
  }
}
```

### 4.4 Define Use Cases

```tsx
// src/useCases/getUserProfile.ts
import { UserProfile } from '../domains/User/userEntity';
import { UserRepository } from '../domains/User/userRepository';

export class GetUserProfile {
  constructor(private userRepository: UserRepository) {}

  async execute(userId: string): Promise<UserProfile> {
    return this.userRepository.getUserById(userId);
  }
}
```

```tsx
// src/useCases/updateUserProfile.ts
import { UserProfile } from '../domains/User/userEntity';
import { UserRepository } from '../domains/User/userRepository';

export class UpdateUserProfile {
  constructor(private userRepository: UserRepository) {}

  async execute(user: UserProfile): Promise<void> {
    await this.userRepository.updateUser(user);
  }
}
```

### 4.5 Setup Infrastructure

```tsx
// src/infrastructure/api/userService.ts
import { UserApi } from '../../interfaces/api/userApi';
import { GetUserProfile } from '../../useCases/getUserProfile';
import { UpdateUserProfile } from '../../useCases/updateUserProfile';

const userApi = new UserApi();
const getUserProfile = new GetUserProfile(userApi);
const updateUserProfile = new UpdateUserProfile(userApi);

export { getUserProfile, updateUserProfile };
```

### 4.6 Setup Redux Slice

```tsx
// src/store/userSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import { UserProfile } from '../domains/User/userEntity';

interface UserState {
  profile: UserProfile | null;
}

const initialState: UserState = {
  profile: null,
};

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    setProfile(state, action: PayloadAction<UserProfile>) {
      state.profile = action.payload;
    },
    clearProfile(state) {
      state.profile = null;
    },
  },
});

export const { setProfile, clearProfile } = userSlice.actions;
export default userSlice.reducer;
```

### 4.7 Create Presentation Components

```tsx
// src/components/Profile.tsx
import React from 'react';
import { UserProfile } from '../domains/User/userEntity';
import { Card, CardContent, Typography, Avatar } from '@mui/material';

interface ProfileProps {
  user: UserProfile;
}

const Profile: React.FC<ProfileProps> = ({ user }) => {
  return (
    <Card>
      <CardContent>
        <Avatar src={user.avatarUrl} alt={user.name} />
        <Typography variant="h5">{user.name}</Typography>
        <Typography variant="body1">{user.headline}</Typography>
        <Typography variant="h6">Experience</Typography>
        <ul>
          {user.experience.map((exp, index) => (
            <li key={index}>{exp}</li>
          ))}
        </ul>
        <Typography variant="h6">Education</Typography>
        <ul>
          {user.education.map((edu, index) => (
            <li key={index}>{edu}</li>
          ))}
        </ul>
      </CardContent>
    </Card>
  );
};

export default Profile;
```

```tsx
// src/components/EditProfile.tsx
import React, { useState } from 'react';
import { UserProfile } from '../domains/User/userEntity';
import { TextField, Button, Grid } from '@mui/material';

interface EditProfileProps {
  user: UserProfile;
  onSave: (updatedUser: UserProfile) => void;
}

const EditProfile: React.FC<EditProfileProps> = ({ user, onSave }) => {
  const [name, setName] = useState<string>(user.name);
  const [headline, setHeadline] = useState<string>(user.headline);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSave({ ...user, name, headline });
  };

  return (
    <form onSubmit={handleSubmit}>
      <Grid container spacing={2}>
        <Grid item xs={12}>
          <TextField label="Name" value={name} onChange={(e) => setName(e.target.value)} fullWidth />
        </Grid>
        <Grid item xs={12}>
          <TextField label="Headline" value={headline} onChange={(e) => setHeadline(e.target.value)} fullWidth />
        </Grid>
        {/* Add fields for experience and education as needed */}
        <Grid item xs={12}>
          <Button type="submit" variant="contained" color="primary">
            Save Profile
          </Button>
        </Grid>
      </Grid>
    </form>
  );
};

export default EditProfile;
```

### 4.8 Create Containers

```tsx
// src/containers/ProfileContainer.tsx
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import Profile from '../components/Profile';
import { getUserProfile } from '../infrastructure/api/userService';
import { setProfile } from '../store/userSlice';
import { RootState } from '../store';

interface ProfileContainerProps {
  userId: string;
}

const ProfileContainer: React.FC<ProfileContainerProps> = ({ userId }) => {
  const dispatch = useDispatch();
  const profile = useSelector((state: RootState) => state.user.profile);
  const [loading, setLoading] = React.useState<boolean>(true);

  useEffect(() => {
    const fetchProfile = async () => {
      const data = await getUserProfile.execute(userId);
      dispatch(setProfile(data));
      setLoading(false);
    };
    fetchProfile();
  }, [dispatch, userId]);

  if (loading) return <div>Loading...</div>;

  return profile ? <Profile user={profile} /> : <div>No Profile Found</div>;
};

export default ProfileContainer;
```

```tsx

// src/containers/EditProfileContainer.tsx
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import EditProfile from '../components/EditProfile';
import { updateUserProfile } from '../infrastructure/api/userService';
import { setProfile } from '../store/userSlice';
import { RootState } from '../store';
import { UserProfile } from '../domains/User/userEntity';

interface EditProfileContainerProps {
  userId: string;
}

const EditProfileContainer: React.FC<EditProfileContainerProps> = ({ userId }) => {
  const dispatch = useDispatch();
  const profile = useSelector((state: RootState) => state.user.profile);

  const handleSave = async (updatedUser: UserProfile) => {
    await updateUserProfile.execute(updatedUser);
    dispatch(setProfile(updatedUser));
    // Additional logic like notifying the user
  };

  return profile ? <EditProfile user={profile} onSave={handleSave} /> : <div>Loading...</div>;
};

export default EditProfileContainer;
```


### 4.9 Utilize Dependency Injection in Pages

```tsx
// pages/profile/[id].tsx
import React from 'react';
import { useRouter } from 'next/router';
import ProfileContainer from '../../containers/ProfileContainer';

const UserProfilePage: React.FC = () => {
  const router = useRouter();
  const { id } = router.query;

  if (typeof id !== 'string') return <div>Invalid User ID</div>;

  return <ProfileContainer userId={id} />;
};

export default UserProfilePage;
```

### 4.10 Applying Design Patterns and SOLID Principles

    Factory Pattern: Used in FeedItemFactory to dynamically render different feed items.
    Clean Architecture: The project is organized into distinct layers (domains, use cases, interfaces, infrastructure, presentation).
    SOLID Principles:
        SRP: Each component and service has a single responsibility.
        OCP: Components like ThemedButton are open for extension via props.
        LSP: ConfirmModal extends BaseModal without altering expected behaviors.
        ISP: Interfaces are segregated into smaller, focused types.
        DIP: Use cases depend on abstractions (UserRepository) rather than concrete implementations (UserApi).


## 5. Conclusion

Implementing Design Patterns, Clean Architecture, and SOLID Principles in your Next.js, MUI, and TypeScript-based LinkedIn clone ensures a well-structured, maintainable, and scalable codebase. Here's a summary of how each concept contributes:

    Design Patterns: Provide reusable solutions for common problems, enhancing code reusability and maintainability.
    Clean Architecture: Ensures separation of concerns, making the system flexible and independent of external factors.
    SOLID Principles: Guide the development of robust, modular, and testable code, facilitating easier maintenance and scalability.

By integrating these principles and patterns, you'll build a robust foundation for your application, enabling efficient development and future growth.
