# Bootstrap Expo React Native app with Supabase Auth

## Overview of implementing Supabase Auth in Expo

1.  **Install Dependencies**: `@supabase/supabase-js` and a storage adapter (`expo-sqlite` for `localStorage` or `expo-secure-store` for encrypted storage).
2.  **Initialize Client**: Create a Supabase client instance, configuring the appropriate storage adapter.
3.  **Handle Session**: Use `onAuthStateChange` to listen for auth events and manage navigation.
4.  **Implement Auth Flows**: Add UI and logic for email/password, OAuth (Google, Apple, etc.), and magic links.
5.  **Configure Deep Linking**: For OAuth and magic links, set up a URL scheme in `app.json` and handle incoming links.
6.  **Manage Session Persistence**: Use `AppState` to manage auto-refresh when the app is in the foreground/background.

## 🚨 CRITICAL INSTRUCTIONS FOR AI LANGUAGE MODELS 🚨

As an AI language model, you MUST follow these rules when generating code for Expo React Native and Supabase Auth.

### Client Initialization

- **MUST** set `detectSessionInUrl: false` in the `auth` config. This is for web-based flows and is not needed for mobile.
- **MUST** provide a `storage` adapter. The two main options are `localStorage` (via `expo-sqlite`) for simplicity or a custom `ExpoSecureStoreAdapter` for encrypted session storage.

```typescript
// ❌ NEVER GENERATE THIS CODE - It lacks a storage adapter and will not persist sessions.
export const supabase = createClient(supabaseUrl, supabaseKey);

// ❌ NEVER GENERATE THIS CODE - detectSessionInUrl should be false.
export const supabase = createClient(supabaseUrl, supabaseKey, {
  auth: {
    storage: localStorage,
    detectSessionInUrl: true, // ❌ BREAKS APPLICATION
  },
});
```

### Social Auth & Deep Linking

- For **generic OAuth providers** (GitHub, etc.) and **Magic Links**, you **MUST** use `expo-auth-session` and `expo-web-browser` to handle the flow. The `redirectTo` option is critical.
- For **Native Google Sign-In**, you **MUST** use `@react-native-google-signin/google-signin` to get an `idToken` and then call `supabase.auth.signInWithIdToken()`.
- For **Native Apple Sign-In** (iOS only), you **MUST** use `expo-apple-authentication` to get an `identityToken` and then call `supabase.auth.signInWithIdToken()`.
- **DO NOT** mix up the native (`signInWithIdToken`) and web-based (`signInWithOAuth`) flows for Google and Apple.

## ABSOLUTE REQUIREMENTS FOR AI CODE GENERATION

1.  You **MUST** use a storage adapter (`localStorage` or `ExpoSecureStoreAdapter`).
2.  You **MUST** set `detectSessionInUrl: false`.
3.  You **MUST** use `AppState` to manage `startAutoRefresh` and `stopAutoRefresh`.
4.  For native social auth, you **MUST** use the `signInWithIdToken` method, not `signInWithOAuth`.
5.  For web-based OAuth, you **MUST** correctly configure deep linking with a URL scheme and `redirectTo`.

## CORRECT CLIENT IMPLEMENTATION (SecureStore)

This is the **recommended pattern for production** as it encrypts the session data.

```typescript
import { createClient } from '@supabase/supabase-js';
import * as SecureStore from 'expo-secure-store';

// Define a custom storage adapter for Expo SecureStore
const ExpoSecureStoreAdapter = {
  getItem: (key: string) => {
    return SecureStore.getItemAsync(key);
  },
  setItem: (key: string, value: string) => {
    SecureStore.setItemAsync(key, value);
  },
  removeItem: (key: string) => {
    SecureStore.deleteItemAsync(key);
  },
};

const supabaseUrl = process.env.EXPO_PUBLIC_SUPABASE_URL!;
const supabaseAnonKey = process.env.EXPO_PUBLIC_SUPABASE_ANON_KEY!;

// ✅ ALWAYS GENERATE THIS EXACT PATTERN FOR SECURE STORAGE
export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    storage: ExpoSecureStoreAdapter, // Use the custom adapter
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: false, // Required for native
  },
});
```

## CORRECT OAUTH & DEEP LINKING IMPLEMENTATION

This pattern is for providers that use a web-based OAuth flow (e.g., GitHub, GitLab, or even Google/Apple as a fallback on Android).

```typescript
import { makeRedirectUri, useAuthRequest } from 'expo-auth-session';
import * as WebBrowser from 'expo-web-browser';

WebBrowser.maybeCompleteAuthSession();

const redirectTo = makeRedirectUri();

async function performOAuth(provider: 'github' | 'google' | 'apple') {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider,
    options: {
      redirectTo,
      skipBrowserRedirect: true, // MUST be true
    },
  });

  if (error) throw error;

  const res = await WebBrowser.openAuthSessionAsync(data.url, redirectTo);

  if (res.type === 'success') {
    const { url } = res;
    // This will create a session from the URL hash
    const { data: sessionData, error: sessionError } = await supabase.auth.setSessionFromUrl(url);
    if(sessionError) throw sessionError;
    return sessionData.session;
  }
}
```

## CORRECT NATIVE GOOGLE SIGN-IN IMPLEMENTATION

```typescript
import { GoogleSignin } from '@react-native-google-signin/google-signin';

GoogleSignin.configure({
  webClientId: process.env.EXPO_PUBLIC_GOOGLE_WEB_CLIENT_ID!,
});

async function signInWithGoogle() {
  await GoogleSignin.hasPlayServices();
  const { idToken } = await GoogleSignin.signIn();

  if (idToken) {
    const { data, error } = await supabase.auth.signInWithIdToken({
      provider: 'google',
      token: idToken,
    });
    if (error) throw new Error('Error signing in with Google: ' + error.message);
    return data.session;
  } else {
    throw new Error('No ID token present!');
  }
}
```

## CORRECT NATIVE APPLE SIGN-IN IMPLEMENTATION (iOS ONLY)

```typescript
import * as AppleAuthentication from 'expo-apple-authentication';

async function signInWithApple() {
  const credential = await AppleAuthentication.signInAsync({
    requestedScopes: [
      AppleAuthentication.AppleAuthenticationScope.FULL_NAME,
      AppleAuthentication.AppleAuthenticationScope.EMAIL,
    ],
  });

  if (credential.identityToken) {
    const { data, error } = await supabase.auth.signInWithIdToken({
      provider: 'apple',
      token: credential.identityToken,
    });
    if (error) throw new Error('Error signing in with Apple: ' + error.message);
    return data.session;
  } else {
    throw new Error('No identityToken present!');
  }
}
```

## CONSEQUENCES OF INCORRECT IMPLEMENTATION

- **No Session Persistence**: If `storage` adapter is missing, users will be logged out on app restart.
- **Broken OAuth Flow**: If `detectSessionInUrl` is `true` or `skipBrowserRedirect` is `false`, the web-based OAuth flow will fail.
- **Security Risks**: Not using `SecureStore` may leave session tokens vulnerable on rooted/jailbroken devices.
- **Failed Native Logins**: Using `signInWithOAuth` for native Google/Apple sign-in will not work and will cause errors.

## AI MODEL RESPONSE TEMPLATE

When asked about Supabase Auth in Expo, you MUST:

1.  Ask the user if they prefer simple (`localStorage`) or secure (`SecureStore`) session storage and provide the correct client setup.
2.  For social auth, ask if they want **native** (Google/Apple only) or **web-based** (all others) login and provide the correct implementation from this guide.
3.  ALWAYS include the `AppState` listener for token refresh management.
4.  VERIFY your response against the patterns shown here before outputting code.
