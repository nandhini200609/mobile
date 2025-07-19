import datetime
class Item:
    id_counter = 1000

    def __init__(self, name, description, cost, quantity):
        self.code = Item.id_counter
        Item.id_counter += 1
        self.name = name
        self.description = description
        self.cost = cost
        self.quantity = quantity

    def __str__(self):
        return f"[{self.code}] {self.name} - {self.description} - ₹{self.cost} (Available: {self.quantity})"
class Category:
    def __init__(self, name):
        self.name = name
        self.items = []

    def add_item(self, item):
        self.items.append(item)

    def get_item_by_code(self, code):
        for item in self.items:
            if item.code == code:
                return item
        return None

    def show_items(self):
        print(f"\nItems in {self.name} Category:")
        for item in self.items:
            print(f"{item}")
class Cart:
    def __init__(self):
        self.cart_items = {}

    def add_to_cart(self, item, quantity):
        if item.quantity < quantity:
            print(f"❌ Only {item.quantity} available.")
            return

        if item.code in self.cart_items:
            self.cart_items[item.code]['quantity'] += quantity
        else:
            self.cart_items[item.code] = {'item': item, 'quantity': quantity}
        item.quantity -= quantity
        print(f"✅ Added {quantity} x {item.name} to cart.")

    def view_cart(self):
        if not self.cart_items:
            print("🛒 Your cart is empty.")
            return 0

        print("\n🛍️ Cart Contents:")
        total = 0
        for code, details in self.cart_items.items():
            item = details['item']
            qty = details['quantity']
            subtotal = item.cost * qty
            total += subtotal
            print(f"[{code}] {item.name} x {qty} = ₹{subtotal}")
        print(f"Total: ₹{total}")
        return total
class Checkout:
    def __init__(self, cart):
        self.cart = cart

    def pay(self):
        if not self.cart.cart_items:
            print("🛒 Cart is empty. Cannot proceed.")
            return

        total = self.cart.view_cart()
        print("\n💳 Choose Payment Method:")
        print("1. UPI\n2. Card\n3. Cash on Delivery")
        option = input("Enter option (1/2/3): ")

        if option not in ('1', '2', '3'):
            print("❌ Invalid payment method.")
            return

        print("✅ Payment successful!")
        self.generate_bill(total, option)

    def generate_bill(self, total, payment_method):
        method = {'1': 'UPI', '2': 'Card', '3': 'Cash on Delivery'}[payment_method]
        now = datetime.datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
        filename = f"FullCart_Bill_{now}.txt"

        with open(filename, "w") as f:
            f.write("==== FullCart Mobile Store ====\n")
            f.write("Mobile: 8248475240\n")
            f.write("https://www.whatsapp.com/catalog/918248475240/app_absent=0\n")
            f.write(f"Date: {datetime.datetime.now()}\n\n")
            f.write("Items Purchased:\n")
            for code, details in self.cart.cart_items.items():
                item = details['item']
                qty = details['quantity']
                subtotal = item.cost * qty
                f.write(f"- [{code}] {item.name} x {qty} = ₹{subtotal}\n")
            f.write(f"\nTotal Amount: ₹{total}\n")
            f.write(f"Payment Mode: {method}\n")
            f.write("\nThank you for shopping with FullCart Mobile Store!\n")

        print(f"🧾 Bill generated and saved as {filename}")
samsung = Category("Samsung")
samsung.add_item(Item("Samsung Galaxy S23", "128GB, 8GB RAM, Phantom Black", 69999, 10))
samsung.add_item(Item("Samsung Galaxy A54", "256GB, 8GB RAM, Awesome Lime", 38999, 12))
samsung.add_item(Item("Samsung Galaxy M14", "128GB, 6GB RAM, Arctic Blue", 13999, 15))
samsung.add_item(Item("Samsung Buds 2 Pro", "Wireless Earbuds", 9999, 20))
samsung.add_item(Item("Samsung Galaxy Book 2 Pro", "Laptop", 69990, 20))

apple = Category("Apple")
apple.add_item(Item("iPhone 14", "128GB, Midnight", 79999, 8))
apple.add_item(Item("iPhone 13", "256GB, Blue", 74999, 10))
apple.add_item(Item("iPhone SE", "64GB, Red", 42999, 12))
apple.add_item(Item("Apple AirPods", "2nd Gen", 10999, 18))

oneplus = Category("OnePlus")
oneplus.add_item(Item("OnePlus 11R", "256GB, 16GB RAM, Galactic Silver", 45999, 10))
oneplus.add_item(Item("OnePlus Nord CE 3", "128GB, 8GB RAM, Aqua Surge", 24999, 14))
oneplus.add_item(Item("OnePlus 10 Pro", "512GB, 12GB RAM, Emerald Forest", 61999, 6))
oneplus.add_item(Item("OnePlus Buds Z2", "Active Noise Cancellation", 4999, 20))

motorola = Category("Motorola")
motorola.add_item(Item("Moto G73", "128GB, 8GB RAM, Midnight Blue", 18999, 10))
motorola.add_item(Item("Moto E13", "64GB, 4GB RAM, Cosmic Black", 7999, 18))

accessories = Category("Accessories")
accessories.add_item(Item("Charger", "18W Fast Charger", 999, 30))
accessories.add_item(Item("Phone Case", "Transparent Shockproof Case", 499, 25))
accessories.add_item(Item("Tempered Glass", "9H Hardness Screen Protector", 299, 40))
accessories.add_item(Item("Wireless Buds", "Universal Bluetooth Buds", 1999, 20))

categories = {
    "samsung": samsung,
    "apple": apple,
    "oneplus": oneplus,
    "motorola": motorola,
    "accessories": accessories
}
cart = Cart()

def shop_flow():
    print("\n🛒 Welcome to FullCart Mobile Store!")

    while True:
        print("\nAvailable Mobile Brands:")
        for idx, key in enumerate(categories.keys(), 1):
            print(f"{idx}. {key.title()}")

        try:
            cat_choice = int(input("Select a brand number: "))
            cat_keys = list(categories.keys())
            if cat_choice < 1 or cat_choice > len(cat_keys):
                print("❌ Invalid choice.")
                continue

            category = categories[cat_keys[cat_choice - 1]]
            while True:
                category.show_items()
                code = int(input("Enter item code to add to cart: "))
                qty = int(input("Quantity: "))
                item = category.get_item_by_code(code)

                if item:
                    cart.add_to_cart(item, qty)
                else:
                    print("❌ Invalid item code.")
        except ValueError:
            print("❌ Invalid input.")
            continue

        more = input("Do you want to add more items? (Y/N): ").strip().upper()
        if more == "Y":
            print("\n1. Go back to same category\n2. Choose another category")
            next_step = input("Choose an option (1/2): ").strip()
            if next_step == "1":
                continue
            elif next_step == "2":
                break
            else:
                print("❌ Invalid choice. Returning to same category.")
                continue
        else:
            print("\n1. Go back to categories\n2. Proceed to checkout")
            next_step = input("Choose an option (1/2): ").strip()
            if next_step == "1":
                continue
            elif next_step == "2":
                print("\n🛍️ Ready to checkout.")
                cart.view_cart()
                checkout = Checkout(cart)
                checkout.pay()
                return
            else:
                print("❌ Invalid choice. Returning to categories.")

if __name__ == "__main__":
    shop_flow()
