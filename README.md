import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Card, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';

// Redux Slice for Cart
const cartSlice = createSlice({
  name: 'cart',
  initialState: [],
  reducers: {
    addToCart: (state, action) => {
      const item = state.find((p) => p.id === action.payload.id);
      if (item) {
        item.quantity += 1;
      } else {
        state.push({ ...action.payload, quantity: 1 });
      }
    },
    removeFromCart: (state, action) => state.filter((p) => p.id !== action.payload),
    increaseQuantity: (state, action) => {
      const item = state.find((p) => p.id === action.payload);
      if (item) item.quantity += 1;
    },
    decreaseQuantity: (state, action) => {
      const item = state.find((p) => p.id === action.payload);
      if (item && item.quantity > 1) item.quantity -= 1;
    },
  },
});

const store = configureStore({ reducer: { cart: cartSlice.reducer } });

const LandingPage = () => (
  <div className="p-10 text-center">
    <h1 className="text-3xl font-bold mb-4">Green Haven</h1>
    <p className="mb-4">Welcome to Green Haven, your one-stop shop for lush, beautiful houseplants.</p>
    <Link to="/products">
      <Button>Get Started</Button>
    </Link>
  </div>
);

const plants = [
  { id: 1, name: 'Snake Plant', price: 20 },
  { id: 2, name: 'Spider Plant', price: 15 },
  { id: 3, name: 'Aloe Vera', price: 25 },
  { id: 4, name: 'Peace Lily', price: 30 },
  { id: 5, name: 'ZZ Plant', price: 35 },
  { id: 6, name: 'Pothos', price: 10 },
];

const ProductList = () => {
  const dispatch = useDispatch();
  return (
    <div className="p-4 grid grid-cols-2 gap-4">
      {plants.map((plant) => (
        <Card key={plant.id} className="p-4">
          <CardContent>
            <h2>{plant.name}</h2>
            <p>Price: ${plant.price}</p>
            <Button onClick={() => dispatch(cartSlice.actions.addToCart(plant))}>Add to Cart</Button>
          </CardContent>
        </Card>
      ))}
    </div>
  );
};

const Cart = () => {
  const cart = useSelector((state) => state.cart);
  const dispatch = useDispatch();
  const total = cart.reduce((acc, item) => acc + item.price * item.quantity, 0);
  return (
    <div className="p-4">
      <h1 className="text-2xl font-bold">Shopping Cart</h1>
      {cart.map((item) => (
        <Card key={item.id} className="p-4 mb-2">
          <CardContent>
            <h2>{item.name}</h2>
            <p>Price: ${item.price}</p>
            <p>Quantity: {item.quantity}</p>
            <Button onClick={() => dispatch(cartSlice.actions.increaseQuantity(item.id))}>+</Button>
            <Button onClick={() => dispatch(cartSlice.actions.decreaseQuantity(item.id))}>-</Button>
            <Button onClick={() => dispatch(cartSlice.actions.removeFromCart(item.id))}>Remove</Button>
          </CardContent>
        </Card>
      ))}
      <p className="mt-4">Total: ${total}</p>
      <Button>Checkout (Coming Soon)</Button>
    </div>
  );
};

const App = () => (
  <Provider store={store}>
    <Router>
      <header className="p-4 bg-green-500 text-white">
        <Link to="/">Home</Link> | <Link to="/cart">Cart</Link>
      </header>
      <Routes>
        <Route path="/" element={<LandingPage />} />
        <Route path="/products" element={<ProductList />} />
        <Route path="/cart" element={<Cart />} />
      </Routes>
    </Router>
  </Provider>
);

export default App;
