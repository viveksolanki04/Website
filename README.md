from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'  # SQLite database
db = SQLAlchemy(app)

# Define the model for the database
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    password = db.Column(db.String(100), nullable=False)

# Sample data for menu items
menu_items = [
    {"name": "Chicken food", "price": 10.99, "image": "static/images/image1.jpg"},
    {"name": "Punjab Foods", "price": 8.99, "image": "static/images/image2.jpg"},
    {"name": "falafal wraps", "price": 19.99, "image": "static/images/image3.jpg"},
    {"name": "gujarat foods", "price": 12.49, "image": "static/images/image4.jpg"},
    {"name": "Italian items", "price": 15.39, "image": "static/images/image5.jpg"},
    {"name": "Non-veg items", "price": 8.99, "image": "static/images/image6.jpg"},
    {"name": "Rajasthan Item", "price": 21.19, "image": "static/images/image7.jpg"},
    {"name": "Dosa And Idli", "price": 17.99, "image": "static/images/image9.jpg"},
]

@app.route('/')
def home():
    return render_template('index.html', menu_items=menu_items)

@app.route('/menu')
def menu():
    return render_template('index.html', section="menu", menu_items=menu_items)

@app.route('/about')
def about():
    return render_template('index.html', section="about")

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']

        # Example login logic (replace with your actual login logic)
        if username == 'your_username' and password == 'your_password':
            return redirect(url_for('dashboard'))
        else:
            return "Invalid username or password. Please try again."

    return render_template('index.html', section="login")

@app.route('/signup', methods=['GET', 'POST'])
def signup():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']

        # Create a new user object
        new_user = User(username=username, password=password)

        # Add the new user to the database
        db.session.add(new_user)
        db.session.commit()

        return "User signed up successfully."

    return render_template('index.html', section="signup")

@app.route('/dashboard')
def dashboard():
    return "Welcome to the dashboard!"

# Close database connections
@app.teardown_appcontext
def shutdown_session(exception=None):
    db.session.remove()



if __name__ == '__main__':
    app.run(debug=True)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meldi Shikotar Food</title>
    <link rel="stylesheet" href="static/css/index.css">
    <style>
         #offerBar {
            display: none;
            position: fixed;
            top: 0;
            width: 100%;
            background: linear-gradient(to right, #ff7e5f, #feb47b);
            color: white;
            text-align: center;
            padding: 15px 0;
            z-index: 1000;
        }

        #offerBar .close {
            position: absolute;
            right: 20px;
            top: 15px;
            font-size: 20px;
            cursor: pointer;
        }

        #offerBar {
            position: fixed;
            top: 0;
            width: 100%;
            background: linear-gradient(to right, #ff7e5f, #feb47b);
            color: white;
            text-align: center;
            padding: 15px 0;
            z-index: 1000;
        }

        #offerBar .close {
            position: absolute;
            right: 20px;
            top: 15px;
            font-size: 20px;
            cursor: pointer;
            color: white;
        }

        /* Profile logo styling */
        #profileLogo {
            position: fixed;
            top: 10px;
            left: 10px;
            z-index: 1000;
            width: 50px; /* Adjust size as needed */
            height: auto; /* Maintain aspect ratio */
        }


        /* General button styling */
        .btn {
            background: linear-gradient(to right, #ff7e5f, #feb47b);
            border: none;
            color: white;
            padding: 10px 20px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 4px 2px;
            cursor: pointer;
            border-radius: 4px;
        }

        .modal {
            display: none;
            position: fixed;
            z-index: 1001;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0, 0, 0, 0.4);
            padding-top: 60px;
        }

        .modal-content {
            background-color: #fefefe;
            margin: 5% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            max-width: 500px;
        }

        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }

        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <body style="background-image: url('path/to/your/image13.jpg');">
        <!-- Your website content here -->
    
    
    
    <div id="offerBar">
        <span>Special Offer: 20% off on all orders above $50! Use code SAVE20 at checkout.</span>
        <span class="close" id="closeOfferBar">&times;</span>
    </div>

    <header>
        <h1>Meldi Shikotar Food</h1>
        <nav>
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#menu">Menu</a></li>
                <li><a href="#about">About</a></li>
                <li><a href="#contact">Contact</a></li>
                <li><a href="#" id="loginLink">Login</a></li>
                <li><a href="#" id="signupLink">Sign Up</a></li>
                <li><a href="#" id="cartLink">Cart (<span id="cartCount">0</span>)</a></li>
            </ul>
        </nav>
    </header>

    <div id="loginModal" class="modal">
        <form id="loginForm" class="modal-content">
            <span class="close" id="closeLoginModal">&times;</span>
            <h2>Login</h2>
            <label for="loginUsername">Username:</label>
            <input type="text" id="loginUsername" name="loginUsername" required><br>
            <label for="loginPassword">Password:</label>
            <input type="password" id="loginPassword" name="loginPassword" required><br>
            <button type="submit">Login</button>
        </form>
    </div>

    <div id="signupModal" class="modal">
        <form id="signupForm" class="modal-content">
            <span class="close" id="closeSignupModal">&times;</span>
            <h2>Sign Up</h2>
            <label for="signupUsername">Username:</label>
            <input type="text" id="signupUsername" name="signupUsername" required><br>
            <label for="signupPassword">Password:</label>
            <input type="password" id="signupPassword" name="signupPassword" required><br>
            <button type="submit">Sign Up</button>
        </form>
    </div>

    <section id="home">
        <div class="hero">
            <h2>Welcome to Meldi Shikotar Food</h2>
            <p>Order delicious food from the comfort of your home</p>
            <a href="#menu" class="btn">View Menu</a>
            <a href="#" class="btn" id="loginButton">Login</a>
            <a href="#" class="btn" id="signupButton">Sign Up</a>
        </div>
    </section>

    <img id="profileLogo" src="path/to/profile/logo.png" alt="Profile Logo">

    <!-- Special Offer Bar -->
    <div id="offerBar">
        <span>Special Offer: 20% off on all orders above $50! Use code SAVE20 at checkout.</span>
        <span class="close" id="closeOfferBar">&times;</span>
    </div>

    <section id="offers" class="bg">
        <h2>Special Offers</h2>
        <div class="offer">
            <h3>Buy One Get One Free</h3>
            <p>Order any pizza and get another one free. Valid on Tuesdays only.</p>
        </div>
        <div class="offer">
            <h3>20% Off on All Orders Above $50</h3>
            <p>Use code SAVE20 at checkout to get a 20% discount.</p>
        </div>
        <div class="offer">
            <h3>Free Dessert with Every Main Course</h3>
            <p>Get a free dessert with every main course ordered. Valid on weekends.</p>
        </div>
    </section>

    <section id="menu" class="bg">
        <h2>Menu</h2>
        {% for item in menu_items %}
        <div class="menu-item">
            <img src="{{ item.image }}" alt="{{ item.name }}">
            <h3>{{ item.name }}</h3>
            <p>{{ item.description }}</p>
            <span>${{ item.price }}</span>
            <button class="add-to-cart" data-item-price="{{ item.price }}">Add to Cart</button>
            
        </div>
        {% endfor %}
    </section>
    
    <section id="about">
        <h2>About Us</h2>
        <img src="static/images/coverimage.jpg" alt="About Us" class="about-image">
    </section>
    
    <section id="contact">
        <h2>Contact Us</h2>
        <p>Email: info@vsfood gmail.co.uk</p>
        <p>Phone: +91-9081501320</p>
    </section>

    <section id="cover" class="bg">
        <div class="cover-content">
            <h2>Welcome to V.S Food</h2>
            <p>Order delicious food from the comfort of your home</p>
            <a href="#menu" class="btn">View Menu</a>
        </div>
    </section>

    <div id="createProfileModal" class="modal">
        <form id="profileForm" class="modal-content">
            <span class="close" id="closeProfileModal">&times;</span>
            <h2>Create Profile</h2>
            <label for="firstName">First Name:</label>
            <input type="text" id="firstName" name="firstName" required><br>
            <label for="lastName">Last Name:</label>
            <input type="text" id="lastName" name="lastName" required><br>
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required><br>
            <label for="phone">Phone:</label>
            <input type="tel" id="phone" name="phone" required><br>
            <label for="address">Address:</label>
            <input type="text" id="address" name="address" required><br>
            <button type="submit">Submit</button>
        </form>
    </div>

    
    <footer>
        <p>&copy; 2024 Meldi Shikotar Food</p>
    </footer>

    <div id="cart">
        <h2>Cart</h2>
        <ul id="cart-items"></ul>
        <p id="cart-total"></p>
        <p id="cart-discount-message" style="color: green;"></p>
        <input type="text" id="voucher-code" placeholder="Enter voucher code">
        <button id="apply-voucher-button">Apply Voucher</button>
        <p id="voucher-message" style="color: blue;"></p>
        <button id="update-cart-button" style="display:none;">Update Cart</button>
        <button id="add-item-button">Add Item</button>
        <button id="remove-item-button">Remove Item</button>
    </div>

    <form id="loginForm" class="login-form" action="action_page.php" method="post">
        <div class="imgcontainer">
            <img src="image avtar.png" alt="Avtar" class="avtar">
        </div>
        <div class="container">
            <label for="uname"><b>Username</b></label>
            <input type="text" placeholder="Enter Username" name="uname" required>
            <label for="psw"><b>Password</b></label>
            <input type="password" placeholder="Enter Password" name="psw" required>
            <button type="submit">Login</button>
            <label>
                <input type="checkbox" checked="checked" name="remember"> Remember me
            </label>
        </div>
        <div id="voucherSection">
            <input type="text" id="voucher-code" placeholder="Enter voucher code">
            <button id="apply-voucher-button">Apply Voucher</button>
            <p id="voucher-message" style="color: blue;"></p>
        </div>
        <div class="container" style="background-color:#f1f1f1">
            <button type="button" class="cancelbtn">Cancel</button>
            <span class="psw">Forgot <a href="#">password?</a></span>
        </div>
        <body><button id="createProfileButton" class="btn">Create Profile</button></body>
    </form>
    
    <script>
        document.addEventListener("DOMContentLoaded", function() {
            // Show the offer bar
            const offerBar = document.getElementById('offerBar');
            offerBar.style.display = 'block';

            // Close the offer bar
            const closeOfferBar = document.getElementById('closeOfferBar');
            closeOfferBar.addEventListener('click', function() {
                offerBar.style.display = 'none';
            });

            // Handle opening and closing of login modal
            const loginModal = document.getElementById('loginModal');
            const loginLink = document.getElementById('loginLink');
            const loginButton = document.getElementById('loginButton');
            const closeLoginModal = document.getElementById('closeLoginModal');

            loginLink.addEventListener('click', function(event) {
                event.preventDefault();
                loginModal.style.display = 'block';
            });

            loginButton.addEventListener('click', function(event) {
                event.preventDefault();
                loginModal.style.display = 'block';
            });

            closeLoginModal.addEventListener('click', function() {
                loginModal.style.display = 'none';
            });

            // Handle opening and closing of signup modal
            const signupModal = document.getElementById('signupModal');
            const signupLink = document.getElementById('signupLink');
            const signupButton = document.getElementById('signupButton');
            const closeSignupModal = document.getElementById('closeSignupModal');

            signupLink.addEventListener('click', function(event) {
                event.preventDefault();
                signupModal.style.display = 'block';
            });

            signupButton.addEventListener('click', function(event) {
                event.preventDefault();
                signupModal.style.display = 'block';
            });

            closeSignupModal.addEventListener('click', function() {
                signupModal.style.display = 'none';
            });

            // Close modals when clicking outside
            window.addEventListener('click', function(event) {
                if (event.target == loginModal) {
                    loginModal.style.display = 'none';
                }
                if (event.target == signupModal) {
                    signupModal.style.display = 'none';
                }
            });

            // Existing cart functionality
            const cart = [];
            let voucherApplied = false;

            // Predefined vouchers for demo purposes
            const vouchers = {
                "SAVE10": 0.1,  // 10% discount
                "SAVE20": 0.2   // 20% discount
            };

            // Function to update the cart count
            function updateCartCount() {
                const cartCount = document.getElementById('cartCount');
                cartCount.textContent = cart.length;
            }

            // Function to add item to cart
            function addItemToCart(itemPrice) {
                cart.push({
                    price: itemPrice,
                    quantity: 1
                });
                updateCartCount();
                displayCartItems();
                calculateCartTotal();
            }

            // Function to remove item from cart
            function removeItemFromCart() {
                cart.pop(); // Remove the last item from the cart
                updateCartCount();
                displayCartItems();
                calculateCartTotal();
            }

            // Add event listeners to add-to-cart button and remove-item button
            const addToCartButtons = document.querySelectorAll('.add-to-cart');
            const removeItemButton = document.getElementById('remove-item-button');
            addToCartButtons.forEach(button => {
                button.addEventListener('click', () => {
                    const itemPrice = parseFloat(button.dataset.itemPrice);
                    addItemToCart(itemPrice);
                });
            });
            removeItemButton.addEventListener('click', removeItemFromCart);

            // Function to display cart items
            function displayCartItems() {
                const cartItems = document.getElementById('cart-items');
                cartItems.innerHTML = '';
                cart.forEach(item => {
                    const li = document.createElement('li');
                    li.textContent = `Item: $${item.price}, Quantity: ${item.quantity}`;
                    cartItems.appendChild(li);
                });
            }

            // Function to calculate the cart total
            function calculateCartTotal() {
                let total = 0;
                cart.forEach(item => {
                    total += item.price * item.quantity;
                });

                const discountMessage = document.getElementById('cart-discount-message');
                const voucherMessage = document.getElementById('voucher-message');
                let discount = 0;

                // Apply discount if total exceeds $50
                if (total > 50) {
                    discount = 0.1; // 10% discount
                    discountMessage.textContent = 'A 10% discount has been applied!';
                } else {
                    discountMessage.textContent = ''; // Clear the discount message if no discount
                }

                // Apply voucher discount if a valid voucher is applied
                if (voucherApplied) {
                    discount += vouchers[document.getElementById('voucher-code').value.toUpperCase()] || 0;
                }

                total *= (1 - discount);

                document.getElementById('cart-total').textContent = `Total: $${total.toFixed(2)}`;
                if (voucherApplied) {
                    voucherMessage.textContent = `Voucher applied: ${(discount * 100).toFixed(0)}% off!`;
                } else {
                    voucherMessage.textContent = '';
                }
            }

            // Function to apply voucher code
            function applyVoucher() {
                const voucherCode = document.getElementById('voucher-code').value.toUpperCase();
                const voucherMessage = document.getElementById('voucher-message');

                if (vouchers[voucherCode]) {
                    voucherApplied = true;
                    voucherMessage.textContent = `Voucher applied: ${voucherCode} (${(vouchers[voucherCode] * 100).toFixed(0)}% off)`;
                } else {
                    voucherApplied = false;
                    voucherMessage.textContent = 'Invalid voucher code';
                }
                calculateCartTotal();
            }

            function createProfile() {
                // Show the create profile modal
                const createProfileModal = document.getElementById('createProfileModal');
                createProfileModal.style.display = 'block';
            }

            // Function to close the create profile modal
            function closeProfileModal() {
                const createProfileModal = document.getElementById('createProfileModal');
                createProfileModal.style.display = 'none';
            }

            // Event listener for the create profile button
            const createProfileButton = document.getElementById('createProfileButton');
            createProfileButton.addEventListener('click', createProfile);

            // Event listener for closing the create profile modal
            const closeProfileModalButton = document.getElementById('closeProfileModal');
            closeProfileModalButton.addEventListener('click', closeProfileModal);
            // Event listener for applying voucher when the input field loses focus
            voucherInput.addEventListener('blur', applyVoucher);
            const voucherInput = document.getElementById('voucher-code');


                // Get form data
                const firstName = document.getElementById('firstName').value;
                const lastName = document.getElementById('lastName').value;
                const email = document.getElementById('email').value;
                const phone = document.getElementById('phone').value;
                const address = document.getElementById('address').value;

                // Create profile object
                const profile = {
                    firstName: firstName,
                    lastName: lastName,
                    email: email,
                    phone: phone,
                    address: address
                };

                // You can further process the profile data here, like sending it to a server

                // For now, log the profile data to the console
                console.log("Profile created:", profile);

                // Close the modal
                closeProfileModal();
            
            // Function to apply voucher code
function applyVoucher() {
    const voucherCode = document.getElementById('voucher-code').value.toUpperCase();
    const voucherMessage = document.getElementById('voucher-message');

    // Check if the voucher code is applicable
    if (vouchers[voucherCode]) {
        // Set discount to 20%
        const voucherDiscount = 0.2;

        voucherApplied = true;
        voucherMessage.textContent = `Voucher applied: ${voucherCode} (20% off)`;

        // Update the total price calculation to reduce the price by 15%
        calculateCartTotal(voucherDiscount);
    } else {
        voucherApplied = false;
        voucherMessage.textContent = 'Invalid voucher code';
        // Call calculateCartTotal without discount if voucher is invalid
        calculateCartTotal();
    }
}

}); 
        
       
    </script>
    </body>
</body>
</html>

body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}
#offers {
    padding: 20px;
    background-color: #f8f8f8;
}
#cover {
    background-image: url('path/to/cover-image13.jpg'); /* Replace 'path/to/cover-image.jpg' with the path to your cover image */
    background-size: cover;
    background-position: center;
    color: #fff; /* Text color */
    padding: 100px 0; /* Adjust padding as needed */
    text-align: center;
}



header {
    background-color: #333;
    color: #fff;
    padding: 20px;
    text-align: center;
}

header h1 {
    margin: 0;
}

nav ul {
    list-style-type: none;
    padding: 0;
}

nav ul li {
    display: inline;
    margin-right: 20px;
}

nav ul li a {
    color: #fff;
    text-decoration: none;
}

section {
    padding: 50px 0;
    text-align: center;
}

.hero {
    background-image: url('images/hero.jpg');
    background-size: cover;
    color: #fff;
    padding: 100px 0;
}

.hero-content {
    max-width: 600px;
    margin: 0 auto;
}

.btn {
    background-color: #FF6347;
    border: none;
    color: #fff;
    padding: 10px 20px;
    text-decoration: none;
    border-radius: 5px;
}

.btn:hover {
    background-color: #FF4500;
}

.menu-container {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.menu-item {
    margin: 20px;
    text-align: center;
    width: 250px;
}

.menu-item img {
    width: 100%;
    height: auto;
    border-radius: 5px;
}

.menu-item h3 {
    margin-top: 10px;
    color: #FF6347;
}

.menu-item p {
    color: #777;
}

.menu-item span {
    display: block;
    color: #333;
    font-weight: bold;
    margin-top: 10px;
}

.menu-item button {
    background-color: #333;
    color: #fff;
    border: none;
    padding: 8px 20px;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 10px;
}

.menu-item button:hover {
    background-color: #555;
}

footer {
    background-color: #333;
    color: #fff;
    padding: 20px;
    text-align: center;
}
/* Additional CSS for basket */
.basket {
    position: fixed;
    top: 10px;
    right: 10px;
    background-color: #fff;
    border: 2px solid #333;
    padding: 10px;
    border-radius: 5px;
    z-index: 999;
}

.basket-item {
    margin-bottom: 10px;
}
.about-image {
    max-width: 50%;
    height: auto;
    margin-top: 20px;

}
/* Add background image to the login and sign-up pages */
#loginModal .modal-content,
#signupModal .modal-content {
    background-image: url('image 11.jpg'); /* Replace 'path/to/login-background-image.jpg' with the path to your login background image */
    background-size: cover;
    background-position: center;
    padding: 20px;
    border-radius: 10px;
}

/* Additional styling for the modal content */
#loginModal .modal-content {
    width: 300px;
}

#signupModal .modal-content {
    width: 350px;
}
#cart {
    position: fixed;
    bottom: 0;
    right: 0;
    width: 300px;
    background-color: #fff;
    border: 1px solid #ddd;
    padding: 16px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
#loginModal .modal-content,
#signupModal .modal-content {
    background-image: url('image avtar.png'); /* Update to match your image filename */
    background-size: cover;
    background-position: center;
    padding: 20px;
    border-radius: 10px;
}
/* Add these styles to your static/css/index.css file */

/* Styles for the offers section */
#offers {
    padding: 20px;
    background-color: #f8f8f8;
}
/* Add these styles to your static/css/index.css file */

/* Styles for the offer bar */
.offer-bar {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    background: linear-gradient(90deg, rgba(255, 123, 0, 1) 0%, rgba(255, 214, 0, 1) 100%);
    color: white;
    text-align: center;
    padding: 15px;
    z-index: 1000;
    display: none;
}

.offer-bar .close {
    position: absolute;
    right: 20px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 20px;
    cursor: pointer;
}

.offer-bar .close:hover {
    color: red;
}

#offers h2 {
    text-align: center;
    margin-bottom: 20px;
}

.offer {
    border: 1px solid #ddd;
    padding: 15px;
    margin: 10px;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.offer h3 {
    margin-top: 0;
}

.offer p {
    margin: 5px 0;
}

/* Styles for special offers in menu items */
.menu-item .special-offer {
    color: red;
    font-weight: bold;
    margin-top: 10px;
}

#cover {
    background-image: url('path/to/cover-image.jpg'); /* Replace 'path/to/cover-image.jpg' with the path to your cover image */
    background-size: cover;
    background-position: center;
    color: #fff; /* Text color */
    padding: 100px 0; /* Adjust padding as needed */
    text-align: center;
}

/* Bordered form */
form {
    border: 3px solid #f1f1f1;
  }
  
  /* Full-width inputs */
  input[type=text], input[type=password] {
    width: 100%;
    padding: 12px 20px;
    margin: 8px 0;
    display: inline-block;
    border: 1px solid #ccc;
    box-sizing: border-box;
  }
  
  /* Set a style for all buttons */
  button {
    background-color: #04AA6D;
    color: white;
    padding: 14px 20px;
    margin: 8px 0;
    border: none;
    cursor: pointer;
    width: 100%;
  }
  
  /* Add a hover effect for buttons */
  button:hover {
    opacity: 0.8;
  }
  
  /* Extra style for the cancel button (red) */
  .cancelbtn {
    width: auto;
    padding: 10px 18px;
    background-color: #f44336;
  }
  
  /* Center the avatar image inside this container */
  .imgcontainer {
    text-align: center;
    margin: 24px 0 12px 0;
  }
  
  /* Avatar image */
  img.avatar {
    width: 40%;
    border-radius: 50%;
  }
  
  /* Add padding to containers */
  .container {
    padding: 16px;
  }
  
  /* The "Forgot password" text */
  span.psw {
    float: right;
    padding-top: 16px;
  }
  
  /* Change styles for span and cancel button on extra small screens */
  @media screen and (max-width: 300px) {
    span.psw {
      display: block;
      float: none;
    }
    .cancelbtn {
      width: 100%;
    }
  }
  body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-image: url('path/to/image10.jpg'); /* Replace 'path/to/image10.jpg' with the path to your background image */
    background-size: cover;
    background-position: center;
}

#cover {
    background-color: rgba(0, 0, 0, 0.5); /* Add a semi-transparent overlay */
    color: #fff; /* Text color */
    padding: 100px 0; /* Adjust padding as needed */
    text-align: center;
}
