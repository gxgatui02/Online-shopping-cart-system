from abc import ABC, abstractmethod

class User(ABC):
    @abstractmethod
    def display_info(self):
        pass

class Product:
    def __init__(self, name, price, quantity):
        self.__name = name
        self.__price = price
        self.__quantity = quantity

    def get_name(self):
        return self.__name

    def get_price(self):
        return self.__price

    def get_quantity(self):
        return self.__quantity

    def set_quantity(self, quantity):
        if quantity >= 0:
            self.__quantity = quantity
        else:
            raise ValueError("Quantity cannot be negative")

class Customer(User):
    def __init__(self, name):
        self.name = name
        self.cart = ShoppingCart()

    def display_info(self):
        print(f"Customer Name: {self.name}")

    def add_to_cart(self, product, quantity):
        if product.get_quantity() < quantity:
            print(f"Only {product.get_quantity()} items of '{product.get_name()}' are available.")
        else:
            self.cart.add_item(product, quantity)
            product.set_quantity(product.get_quantity() - quantity)
            print(f"Added {quantity} of '{product.get_name()}' to the cart.")

    def remove_from_cart(self, product):
        self.cart.remove_item(product)

class PremiumCustomer(Customer):
    def __init__(self, name, discount):
        super().__init__(name)
        self.discount = discount

    def calculate_total(self):
        total = self.cart.calculate_total()
        return total * (1 - self.discount / 100)

class ShoppingCart:
    def __init__(self):
        self.items = {}

    def add_item(self, product, quantity):
        if product in self.items:
            self.items[product] += quantity
        else:
            self.items[product] = quantity

    def remove_item(self, product):
        if product in self.items:
            product.set_quantity(product.get_quantity() + self.items[product])
            del self.items[product]
            print(f"Removed '{product.get_name()}' from the cart.")
        else:
            print(f"'{product.get_name()}' is not in the cart.")

    def calculate_total(self):
        total = 0
        for product, quantity in self.items.items():
            total += product.get_price() * quantity
        return total

    def is_empty(self):
        return len(self.items) == 0

# Example Usage
def main():
    product1 = Product("Laptop", 1200, 5)
    product2 = Product("Phone", 800, 2)

    customer = Customer("John")
    customer.add_to_cart(product1, 1)
    customer.add_to_cart(product2, 3)  # Try to add more than available stock

    if not customer.cart.is_empty():
        print(f"Total price: ${customer.cart.calculate_total()}")
    else:
        print("Your cart is empty.")

    premium_customer = PremiumCustomer("Alice", 10)
    premium_customer.add_to_cart(product1, 1)

    if not premium_customer.cart.is_empty():
        print(f"Discounted price for premium customer: ${premium_customer.calculate_total()}")
    else:
        print("Your cart is empty.")

if __name__ == "__main__":
    main()

