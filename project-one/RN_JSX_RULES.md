## How Navigation works
To navigate from one screen to another screen.<br>
```tsx
<View style={styles.container}>
  <Text style={styles.content}>
    Edit src/app/index.tsx to edit this screen
  </Text>
  <Link href="/about">Visit about screen</Link>
</View>
```
This will navigate to the about screen.<br>
The reason we have the stack like navigation is we used the `Stack` component in the root layout.
```tsx
import { Stack } from "expo-router";

export default function RootLayout() {
  return <Stack />;
}
```
<br>
There is a browser-tab like name above the view/window where the name of the view is written. If you want to get rid of this, add the following prop to the `Stack` component:
```tsx
<Stack screenOptions={{ headerShown: false }} />
```
The only problem it has is the view content overflows the curved edges.
