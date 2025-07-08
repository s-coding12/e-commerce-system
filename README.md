# e-commerce-system
from datetime import date

class RegProducts:
    def __init__(self, Name, Price, Quant):
        self.Name = Name
        self.Price = Price
        self.Quant = Quant

    def ExpirAble(self):
        return False

    def ShippAble(self):
        return False



class ExpProducts(RegProducts):
    def __init__(self, Name, Price, Quant, expiry_date):
        super().__init__(Name, Price, Quant)
        self.expiry_date = expiry_date

    def ExpirAble(self):
        return date.today() > self.expiry_date
    
    def ShippAble(self):
        return False
    


class ShippProducts(RegProducts):
    def __init__(self, Name, Price, Quant, weight):
        super().__init__(Name, Price, Quant)
        self.weight = weight

    def ShippAble(self):
        return True

    def get_Name(self):
        return self.Name

    def get_Weight(self):
        return self.weight


class Exp_Sh_able_Products(ExpProducts):
    def __init__(self, Name, Price, Quant, expiry_date, weight):
        super().__init__(Name, Price, Quant, expiry_date)
        self.weight = weight

    def ShippAble(self):
        return True

    def get_Name(self):
        return self.Name

    def get_Weight(self):
        return self.weight
    
class Customer:
    def __init__(self, Name, Balance):
        self.Name = Name
        self.Balance = Balance



class Shipping_Service:
    def ship(self, items):
        total_weight = 0
        print("\n Shipping this items:")
        for product, Quant in items:
            weight = product.get_Weight() * Quant
            print(f"• {Quant}x {product.get_Name()} — {weight:.2f} kg")
            total_weight += weight
        print(f"Total shipment weight: {total_weight:.2f} kg\n")




class Cart:
    def __init__(self):
        self.items = []  

    def add(self, product, quantity):
        if product.Quant < quantity:
            print(f"this quantity isn't available .")
            return
        if product.ExpirAble():
            print(f"this product is expired ,sorry can't add it.")
            return
        self.items.append((product, quantity))
        print(f" {quantity}x {product.Name} added to cart successfully.")

    def is_empty(self):
        return not self.items

    def checkout(self, customer):
        if self.is_empty():
            print("Your Cart is empty go and add some items .")
            return

        subtotal = 0
        shipping_items = []

        for product, Quant in self.items:
            if product.Quant < Quant:
                print(f"this quantity isn't available.")
                return
            if product.ExpirAble():
                print(f"this product is expired ,sorry can't add it. ")
                return
            subtotal += product.Price * Quant
            if product.ShippAble():
                shipping_items.append((product, Quant))

        shipping_fee = 25 if shipping_items else 0
        total = subtotal + shipping_fee

        if customer.Balance < total:
            print("sorry insufficient balance can't pay.")
            return

        customer.Balance -= total
        for product, Quant in self.items:
            product.Quant -= Quant

        print("\n--- Checkout ---")
        for product, Quant in self.items:
            print(f"{Quant}x {product.Name} — {product.Price * Quant} LE")
        print(f"Subtotal: {subtotal} LE")
        print(f"Shipping: {shipping_fee} LE")
        print(f"Totalprice: {total} LE")
        print(f"Your Balance now: {customer.Balance} LE\n")

        if shipping_items:
            Shipping_Service().ship(shipping_items)

products = [
    Exp_Sh_able_Products("cheese", 100, 5, date(2025, 12, 1), 1),
    ShippProducts("tv", 25000, 2, 10),
    Exp_Sh_able_Products("biscuit", 20, 10, date(2025, 10, 10), 0.25),
    Exp_Sh_able_Products("Milk", 50, 10, date(2025, 11, 1), 1.5),
    ExpProducts("bread" , 10 , 50 , date(2025, 7, 10)),
    RegProducts("scratch card", 25, 50)
]
cart = Cart()
customer1 = Customer("jack", 30000)


cart.add(products[0], 6)  # cheese
cart.add(products[1], 1)  # tv
cart.add(products[2], 1)  # biscuit


cart.checkout(customer1)

customer2=Customer("moly",500)

cart.add(products[3], 6)  # milk
cart.add(products[4], 1)  # bread
cart.add(products[5], 1)  # scratch card

cart.checkout(customer2)
