# RasoiRoots
website for rasoi roots home-made products 
const express = require('express');
const fs = require('fs');
const cors = require('cors');
const bodyParser = require('body-parser');
const app = express();
const PORT = 3000;

app.use(cors());
app.use(bodyParser.json());
app.use(express.static('public'));

let users = require('./data/users.json');
let products = require('./data/products.json');

app.post('/api/login', (req, res) => {
  const { email, password } = req.body;
  const user = users.find(u => u.email === email && u.password === password);
  if (user) res.json({ status: 'success', user });
  else res.status(401).json({ status: 'error', message: 'Invalid credentials' });
});

app.post('/api/signup', (req, res) => {
  const { name, email, password } = req.body;
  if (users.find(u => u.email === email)) {
    return res.status(400).json({ status: 'error', message: 'Email already exists' });
  }
  const newUser = { name, email, password };
  users.push(newUser);
  fs.writeFileSync('./data/users.json', JSON.stringify(users, null, 2));
  res.json({ status: 'success', user: newUser });
});

app.get('/api/products', (req, res) => {
  res.json(products);
});

app.post('/api/products', (req, res) => {
  const product = req.body;
  products.push(product);
  fs.writeFileSync('./data/products.json', JSON.stringify(products, null, 2));
  res.json({ status: 'success', product });
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
[]
[
  { "name": "Red Chilly Powder", "price": 70 },
  { "name": "Coriander Powder", "price": 60 },
  { "name": "Turmeric Powder", "price": 70 },
  { "name": "Garam Masala", "price": 200 },
  { "name": "Cumin Seeds", "price": 90 },
  { "name": "Besan", "price": 100 },
  { "name": "Mustard Oil", "price": 160 }
]
body {
  font-family: 'Segoe UI', sans-serif;
  margin: 0;
  background: #fef8f3;
  color: #333;
}
header, nav, footer {
  background-color: #a83232;
  color: white;
  padding: 15px;
  text-align: center;
}
nav a {
  margin: 0 15px;
  color: white;
  text-decoration: none;
  font-weight: bold;
}
.container {
  padding: 20px;
}
.product, .review {
  background: #fff5ee;
  padding: 15px;
  margin: 10px 0;
  border-radius: 8px;
}
button {
  background-color: #a83232;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
  border-radius: 5px;
}
input {
  padding: 10px;
  margin: 5px;
  width: 90%;
}
iframe {
  width: 100%;
  height: 400px;
  border: none;
}
async function loadProducts() {
  const res = await fetch('/api/products');
  const data = await res.json();
  const container = document.getElementById('product-list');
  data.forEach(p => {
    const div = document.createElement('div');
    div.className = 'product';
    div.innerHTML = `<h3>${p.name}</h3><p>Price: ₹${p.price}</p><button onclick="addToCart('${p.name}')">Add to Cart</button>`;
    container.appendChild(div);
  });
}

let cart = [];

function addToCart(item) {
  cart.push(item);
  alert(`${item} added to cart`);
  localStorage.setItem('cart', JSON.stringify(cart));
}

function loadCart() {
  cart = JSON.parse(localStorage.getItem('cart') || '[]');
  const container = document.getElementById('cart-items');
  cart.forEach((item, i) => {
    const div = document.createElement('div');
    div.textContent = `${i + 1}. ${item}`;
    container.appendChild(div);
  });
}

function checkout() {
  if (cart.length === 0) {
    alert('Your cart is empty.');
    return;
  }
  alert(`Order placed: ${cart.join(', ')}`);
  cart = [];
  localStorage.removeItem('cart');
  location.reload();
}
<!DOCTYPE html>
<html>
<head><title>Home</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>RasoiRoots</h1></header>
<nav>
  <a href="index.html">🏠 Home</a>
  <a href="products.html">🛍️ Products</a>
  <a href="cart.html">🛒 Cart</a>
  <a href="login.html">🔐 Login</a>
  <a href="signup.html">✍️ Sign Up</a>
  <a href="admin.html">⚙️ Admin</a>
  <a href="reviews.html">💬 Reviews</a>
  <a href="contact.html">📍 Contact</a>
</nav>
<div class="container">
  <h2>Welcome to RasoiRoots</h2>
  <p>Pure & Homemade Spices With Authentic Flavour!</p>
</div>
<footer>&copy; 2025 RasoiRoots</footer>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Cart</title><link rel="stylesheet" href="style.css"><script defer src="script.js"></script></head>
<body onload="loadCart()">
<header><h1>Your Cart</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <div id="cart-items"></div>
  <button onclick="checkout()">Checkout</button>
</div>
<footer>&copy; 2025 RasoiRoots</footer>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Login</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>Login</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <input type="email" id="email" placeholder="Email">
  <input type="password" id="password" placeholder="Password">
  <button onclick="login()">Login</button>
</div>
<script>
async function login() {
  const res = await fetch('/api/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      email: document.getElementById('email').value,
      password: document.getElementById('password').value
    })
  });
  const data = await res.json();
  if (data.status === 'success') alert('Login successful!');
  else alert('Login failed');
}
</script>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Sign Up</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>Sign Up</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <input type="text" id="name" placeholder="Name">
  <input type="email" id="email" placeholder="Email">
  <input type="password" id="password" placeholder="Password">
  <button onclick="signup()">Register</button>
</div>
<script>
async function signup() {
  const res = await fetch('/api/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      name: document.getElementById('name').value,
      email: document.getElementById('email').value,
      password: document.getElementById('password').value
    })
  });
  const data = await res.json();
  if (data.status === 'success') alert('Signup successful!');
  else alert('Signup failed');
}
</script>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Admin</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>Admin Panel</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <input type="text" id="productName" placeholder="Product Name">
  <input type="number" id="productPrice" placeholder="Price">
  <button onclick="addProduct()">Add Product</button>
</div>
<script>
async function addProduct() {
  const name = document.getElementById('productName').value;
  const price = document.getElementById('productPrice').value;
  const res = await fetch('/api/products', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name, price })
  });
  const data = await res.json();
  if (data.status === 'success') alert('Product added!');
}
</script>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Reviews</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>Customer Reviews</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <div class="review">"Absolutely love the freshness of RasoiRoots spices!" – Priya</div>
  <div class="review">"Best homemade masalas, feels like maa ke haath ka swad!" – Aarav</div>
</div>
<footer>&copy; 2025 RasoiRoots</footer>
</body>
</html>
<!DOCTYPE html>
<html>
<head><title>Contact</title><link rel="stylesheet" href="style.css"></head>
<body>
<header><h1>Contact Us</h1></header>
<nav><a href="index.html">🏠 Home</a></nav>
<div class="container">
  <p>📞 8954152963, 9411911398</p>
  <p>🏡 Village- Atmadsarai, 203408, Bulandshahr</p>
  <p>📧 rasoiroots@gmail.com</p>
  <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d28054.647515250086!2d77.82529174999999!3d28.4846376!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x390ca3772100734b%3A0x2b85934592a389b9!2sBrajpal%20Madhyan-%20oil%20mill%20%2C%20flour%20mill%20and%20spices%20point.!5e0!3m2!1sen!2sin!4v1752165381607!5m2!1sen!2sin" allowfullscreen loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
</div>
<footer>&copy; 2025 RasoiRoots</footer>
</body>
</html>

