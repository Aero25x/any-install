# Fast way to create React Native empty application with Expo and Redux


```bash
# Step 1: Prompt for app name
read -p "Enter your app name: " APP_NAME

# Step 2: Create a new Expo project
npx create-expo-app $APP_NAME
cd $APP_NAME

# Step 3: Install Redux and related dependencies
npm install redux react-redux @reduxjs/toolkit

# Step 4: Create folder structure for Redux
mkdir -p src/store src/slices src/components

# Step 5: Create the Redux store configuration file
cat > src/store/store.js << 'EOF'
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
  reducer: {
    // Add your reducers here
  },
});

export default store;
EOF

# Step 6: Create an example slice (using Redux Toolkit)
cat > src/slices/exampleSlice.js << 'EOF'
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  value: 0,
};

const exampleSlice = createSlice({
  name: 'example',
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = exampleSlice.actions;
export default exampleSlice.reducer;
EOF

# Step 7: Update store to include the example slice
cat > src/store/store.js << 'EOF'
import { configureStore } from '@reduxjs/toolkit';
import exampleReducer from '../slices/exampleSlice';

const store = configureStore({
  reducer: {
    example: exampleReducer,
  },
});

export default store;
EOF

# Step 8: Update App.js to include Redux Provider
cat > App.js << 'EOF'
import React from 'react';
import { Provider } from 'react-redux';
import { StyleSheet, View } from 'react-native';
import store from './src/store/store';
import ExampleComponent from './src/components/ExampleComponent';

export default function App() {
  return (
    <Provider store={store}>
      <View style={styles.container}>
        <ExampleComponent />
      </View>
    </Provider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
});
EOF

# Step 9: Create example component
cat > src/components/ExampleComponent.js << 'EOF'
import React from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from '../slices/exampleSlice';

const ExampleComponent = () => {
  const count = useSelector((state) => state.example.value);
  const dispatch = useDispatch();

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Count: {count}</Text>
      <View style={styles.buttonContainer}>
        <Button title="Increment" onPress={() => dispatch(increment())} />
      </View>
      <View style={styles.buttonContainer}>
        <Button title="Decrement" onPress={() => dispatch(decrement())} />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  text: {
    fontSize: 32,
    fontWeight: 'bold',
    marginBottom: 30,
  },
  buttonContainer: {
    marginVertical: 10,
    width: 200,
  },
});

export default ExampleComponent;
EOF

echo ""
echo "✅ Setup complete for $APP_NAME with Expo!"
echo ""
echo "To run the app:"
echo "  npx expo start"
echo ""
echo "Then scan the QR code with:"
echo "  - Expo Go app on iOS/Android"
echo "  - Or press 'w' for web browser"
echo "  - Or press 'a' for Android emulator"
echo "  - Or press 'i' for iOS simulator"
echo ""
```
