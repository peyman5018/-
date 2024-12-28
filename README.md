# -
برنامه فروشگاهی
import React, { useState, useEffect } from 'react';
import { View, Text, Button, FlatList, TouchableOpacity, Alert, TextInput } from 'react-native';
import axios from 'axios';

// نام برنامه به Satish تغییر داده شد.
const App = () => {
  const [products, setProducts] = useState([]);
  const [cart, setCart] = useState([]);
  const [user, setUser] = useState(null);
  const [orderHistory, setOrderHistory] = useState([]);

  // دریافت محصولات از API
  useEffect(() => {
    fetchProducts();
  }, []);

  const fetchProducts = async () => {
    try {
      const response = await axios.get('http://your-flask-api-url/products'); // آدرس API محصولات شما
      setProducts(response.data);
    } catch (error) {
      console.error(error);
      Alert.alert('Error', 'Failed to fetch products');
    }
  };

  // افزودن محصول به سبد خرید
  const addToCart = (product) => {
    setCart([...cart, product]);
    Alert.alert('Added to Cart', `${product.name} has been added to your cart.`);
  };

  // ثبت نام کاربر
  const registerUser = (email, password) => {
    const newUser = { email, password };
    setUser(newUser);
    Alert.alert('Registration Success', 'You have been successfully registered!');
  };

  // ورود به حساب کاربری
  const loginUser = (email, password) => {
    if (user && user.email === email && user.password === password) {
      Alert.alert('Login Success', 'Welcome back!');
    } else {
      Alert.alert('Login Failed', 'Incorrect email or password');
    }
  };

  // ثبت سفارش
  const placeOrder = () => {
    if (cart.length === 0) {
      Alert.alert('Cart Empty', 'Please add products to your cart before placing an order.');
      return;
    }

    const order = { cart, user };
    setOrderHistory([...orderHistory, order]);
    setCart([]);
    Alert.alert('Order Placed', 'Your order has been successfully placed.');
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Text style={{ fontSize: 24, fontWeight: 'bold' }}>Satish Store</Text>
      
      {/* فرم ثبت نام */}
      <TextInput
        style={{ height: 40, borderColor: 'gray', borderWidth: 1, marginBottom: 10, paddingLeft: 10 }}
        placeholder="Enter email"
        onChangeText={(text) => setUser({ ...user, email: text })}
      />
      <TextInput
        style={{ height: 40, borderColor: 'gray', borderWidth: 1, marginBottom: 10, paddingLeft: 10 }}
        placeholder="Enter password"
        secureTextEntry
        onChangeText={(text) => setUser({ ...user, password: text })}
      />
      <Button title="Register" onPress={() => registerUser(user.email, user.password)} />

      {/* فرم ورود */}
      <TextInput
        style={{ height: 40, borderColor: 'gray', borderWidth: 1, marginTop: 20, paddingLeft: 10 }}
        placeholder="Enter email"
        onChangeText={(text) => setUser({ ...user, email: text })}
      />
      <TextInput
        style={{ height: 40, borderColor: 'gray', borderWidth: 1, marginBottom: 10, paddingLeft: 10 }}
        placeholder="Enter password"
        secureTextEntry
        onChangeText={(text) => setUser({ ...user, password: text })}
      />
      <Button title="Login" onPress={() => loginUser(user.email, user.password)} />

      {/* نمایش محصولات */}
      <FlatList
        data={products}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={{ padding: 10, marginBottom: 10, borderColor: 'gray', borderWidth: 1 }}>
            <Text>{item.name}</Text>
            <Text>{item.description}</Text>
            <Text>{item.price} USD</Text>
            <TouchableOpacity onPress={() => addToCart(item)}>
              <Text style={{ color: 'blue' }}>Add to Cart</Text>
            </TouchableOpacity>
          </View>
        )}
      />

      {/* نمایش سبد خرید */}
      <View style={{ marginTop: 20 }}>
        <Text>Shopping Cart:</Text>
        {cart.length === 0 ? (
          <Text>No items in the cart.</Text>
        ) : (
          cart.map((item, index) => (
            <Text key={index}>{item.name} - {item.price} USD</Text>
          ))
        )}
        <Button title="Place Order" onPress={placeOrder} />
      </View>
    </View>
  );
};

export default App;
