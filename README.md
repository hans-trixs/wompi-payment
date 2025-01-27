<<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pasarela de Pago</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 500px;
            margin: 20px auto;
            padding: 20px;
        }
        .size-container {
            margin: 20px 0;
        }
        .size-grid {
            display: grid;
            grid-template-columns: repeat(6, 1fr);
            gap: 10px;
            margin-top: 10px;
        }
        .size-button {
            aspect-ratio: 1;
            border: 1px solid #ccc;
            background: white;
            cursor: pointer;
            font-weight: bold;
        }
        .size-button.selected {
            background: black;
            color: white;
        }
        .quantity-container {
            margin: 20px 0;
        }
        .quantity-control {
            display: flex;
            align-items: center;
            max-width: 150px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
        .quantity-button {
            padding: 10px 15px;
            background: none;
            border: none;
            cursor: pointer;
        }
        .quantity-input {
            width: 50px;
            text-align: center;
            border: none;
            border-left: 1px solid #ccc;
            border-right: 1px solid #ccc;
            padding: 5px;
        }
        .pay-button {
            width: 100%;
            padding: 15px;
            background: black;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 20px;
        }
        .pay-button:disabled {
            background: #ccc;
            cursor: not-allowed;
        }
        .total {
            margin-top: 20px;
            padding-top: 20px;
            border-top: 1px solid #ccc;
            display: flex;
            justify-content: space-between;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="size-container">
            <label>Size:</label>
            <div class="size-grid">
                <button class="size-button" data-size="S">S</button>
                <button class="size-button" data-size="M">M</button>
                <button class="size-button" data-size="L">L</button>
                <button class="size-button" data-size="XL">XL</button>
            </div>
        </div>

        <div class="quantity-container">
            <label>Cantidad:</label>
            <div class="quantity-control">
                <button class="quantity-button" onclick="decrementQuantity()">-</button>
                <input type="number" class="quantity-input" value="1" min="1" onchange="updateQuantity(this.value)">
                <button class="quantity-button" onclick="incrementQuantity()">+</button>
            </div>
        </div>

        <div class="total">
            <span>Total:</span>
            <span id="total-price">$54,900 COP</span>
        </div>

        <button class="pay-button" onclick="handlePayment()" disabled>
            Pagar con Wompi
        </button>
    </div>

    <script>
        const PRICE = 50000; // Ajusta este precio según tu producto
        const WOMPI_KEY = 'pub_prod_IHoK7F2uGj8oHxezXJ9yqhlxYO17tOMv';
        let selectedSize = '';
        let quantity = 1;

        // Manejador de selección de talla
        document.querySelectorAll('.size-button').forEach(button => {
            button.addEventListener('click', () => {
                document.querySelectorAll('.size-button').forEach(btn => {
                    btn.classList.remove('selected');
                });
                button.classList.add('selected');
                selectedSize = button.dataset.size;
                updatePayButton();
            });
        });

        function updateQuantity(value) {
            quantity = Math.max(1, parseInt(value) || 1);
            document.querySelector('.quantity-input').value = quantity;
            updateTotal();
        }

        function incrementQuantity() {
            updateQuantity(quantity + 1);
        }

        function decrementQuantity() {
            updateQuantity(quantity - 1);
        }

        function updateTotal() {
            const total = PRICE * quantity;
            document.getElementById('total-price').textContent = 
                `$${total.toLocaleString()} COP`;
        }

        function updatePayButton() {
            const payButton = document.querySelector('.pay-button');
            payButton.disabled = !selectedSize;
        }

        function handlePayment() {
            if (!selectedSize) return;

            const total = PRICE * quantity;
            const reference = `ORDER-${Date.now()}`;
            
            const wompiUrl = `https://checkout.wompi.co/p/?` +
                `public-key=${WOMPI_KEY}&` +
                `currency=COP&` +
                `amount-in-cents=${total * 100}&` +
                `reference=${reference}&` +
                `shipping_metadata=${JSON.stringify({
                    size: selectedSize,
                    quantity: quantity
                })}`;

            window.location.href = wompiUrl;
        }
    </script>
</body>
</html>