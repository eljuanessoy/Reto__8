# Reto__7
By Juan Esteban Molina Rey (eljuanessoy)

### 1. The restaurant class revisted like for the third time.
- Add the proper data structure to manage multiple orders (maybe a FIFO queue)
- Define a named tuple somewhere in the menu, e.g. to define a set of items.
- Create an interface in the order class, to create a new menu, aggregate the functions for add, update, delete items. All the menus should be stored as JSON files. (use dicts for this task.)

```python
import json
from collections import deque, namedtuple

# Definir una NamedTuple para representar un item del menú
MenuItemTuple = namedtuple("MenuItem", ["nombre", "precio", "impuesto", "propina"])

class Menu:
    def __init__(self, filename="menu.json"):
        self.filename = filename
        self.items = self.load_menu()

    def load_menu(self):
        try:
            with open(self.filename, "r") as file:
                return json.load(file)
        except (FileNotFoundError, json.JSONDecodeError):
            return {}

    def save_menu(self):
        with open(self.filename, "w") as file:
            json.dump(self.items, file, indent=4)

    def add_item(self, category, item):
        if category not in self.items:
            self.items[category] = []
        self.items[category].append(item._asdict())
        self.save_menu()

    def update_item(self, category, old_name, new_item):
        if category in self.items:
            for i, item in enumerate(self.items[category]):
                if item["nombre"] == old_name:
                    self.items[category][i] = new_item._asdict()
                    self.save_menu()
                    return True
        return False

    def delete_item(self, category, name):
        if category in self.items:
            self.items[category] = [item for item in self.items[category] if item["nombre"] != name]
            self.save_menu()
            return True
        return False

class MenuItem:
    def __init__(self, nombre, precio, impuesto, propina):
        self.precio = precio
        self.impuesto = impuesto
        self.propina = propina
        self.nombre = nombre

    def calcularPrecio(self):
        impuesto = self.precio * self.impuesto
        propina = self.precio * self.propina
        total = self.precio + impuesto + propina
        return total
    
class Entrada(MenuItem):
    def __init__(self, nombre, precio, impuesto, propina, tipo):
        super().__init__(nombre, precio, impuesto, propina)
        self.tipo = tipo

class Postre(MenuItem):
    def __init__(self, nombre, precio, impuesto, propina, tipo):
        super().__init__(nombre, precio, impuesto, propina)
        self.tipo = tipo

class Bebida(MenuItem):
    def __init__(self, nombre, precio, impuesto, propina, tipo):
        super().__init__(nombre, precio, impuesto, propina)
        self.tipo = tipo

class Ensalada(MenuItem):
    def __init__(self, nombre, precio, impuesto, propina, vinagreta):
        super().__init__(nombre, precio, impuesto, propina)
        self.vinagreta = vinagreta

class Fuerte(MenuItem):
    def __init__(self, nombre, precio, impuesto, propina, tipo):
        super().__init__(nombre, precio, impuesto, propina)
        self.tipo_pasta = tipo

class Order:
    def __init__(self):
        self.items = []
    
    def agregarItem(self, item):
        self.items.append(item)

    def calcularTotal(self):
        total = sum(item.calcularPrecio() for item in self.items)
        return total

    def imprimirFactura(self):
        for item in self.items:
            print(f"{item.nombre} - $ {item.calcularPrecio()}")
        print("Total: $", self.calcularTotal())

class OrderManager:
    def __init__(self):
        self.orders = deque()
    
    def add_order(self, order):
        self.orders.append(order)
    
    def process_order(self):
        if self.orders:
            order = self.orders.popleft()
            order.imprimirFactura()
        else:
            print("No hay órdenes en la cola.")

# Creación de elementos del menú
entrada1 = Entrada("Cacerola de Patatas Bravas", 56800, 0.7, 0.10, "Queso")
entrada2 = Entrada("Ración de Jamón Ibérico de Bellota", 223500, 0.7, 0.10, "Carne")
entrada3 = Entrada("Carpaccio de Salmón", 70000, 0.7, 0.10, "Pescado")

fuerte1 = Fuerte("Spaguetti a la Fiorentina", 44600, 0.7, 0.10, "Spaghetti")
fuerte2 = Fuerte("Pasta Pomodoro", 70000, 0.7, 0.10, "Pasta corta")
fuerte3 = Fuerte("Spaguetti al Guanciale", 75000, 0.7, 0.10, "Spaghetti")
fuerte4 = Fuerte("Pasta a la Rueda con Prosciutto y manzana confitada", 85000, 0.7, 0.10, "Fetuccini")
fuerte5 = Fuerte("Paella Mixta", 149800, 0.7, 0.10, "Paella")

postre1 = Postre('Torta Philadelphia', 29800, 0.7, 0.10, "Tarta")
postre2 = Postre("Copa Amaretto", 26000, 0.7, 0.10, "Copa")
postre3 = Postre("Gelato al Pistacchio", 40000, 0.7, 0.10, "Gelatina")
postre4 = Postre("Afogatto", 26000, 0.7, 0.10, "Helado")

bebida1 = Bebida("Bubble Fizz", 50000, 0.7, 0.10, "Licor")
bebida2 = Bebida("Capuccino", 10000, 0.7, 0.10, "Bebida caliente")
bebida3 = Bebida("Gassata de Frutos Verdes", 18900, 0.7, 0.10, 'Refresco')
bebida4 = Bebida("Limonada de Mango Biche", 16500, 0.7, 0.10, "Limonada")
bebida5 = Bebida("Jugo de Mora", 10000, 0.7, 0.10, "Jugo")

ensalada1 = Ensalada("Insalata Di garedino", 55000, 0.7, 0.10, "Vinagreta Oliva Limón")
ensalada2 = Ensalada("Vero Amore", 51000, 0.7, 0.10, "Vinagreta tradicional")



# Crea una nueva orden
orden1 = Order()
orden1.agregarItem(ensalada2)
orden1.agregarItem(fuerte3)
orden1.agregarItem(postre3)
orden1.agregarItem(bebida4)

orden2 = Order()
orden2.agregarItem(entrada2)
orden2.agregarItem(fuerte5)
orden2.agregarItem(postre1)
orden2.agregarItem(bebida3)

orden3 = Order()
orden3.agregarItem(entrada1)
orden3.agregarItem(fuerte1)
orden3.agregarItem(postre4)
orden3.agregarItem(bebida3)

# Imprime el menú con su descuento si existe
orden = int(input("Ingrese una orden, 1, 2, 3: "))

if orden == 1:
    print()
    print("La factura de la orden 1 es:")
    print()
    orden1.aplicarDescuento()
    orden1.imprimirFactura()
    print()

elif orden == 2:
    print()
    print("La factura de la orden 2 es:")
    print()
    orden2.aplicarDescuento()
    orden2.imprimirFactura()
    print()

elif orden == 3:
    print()
    print("La factura de la orden 3 es:")
    print()
    orden3.aplicarDescuento()
    orden3.imprimirFactura()
    print()

else:
    print("Ingrese un método válido")

metodo = int(input("Elige un método de pago (1 si es con efectivo, 2 si es con tarjeta): "))

if metodo == 1:
    total = orden1.calcularTotal()
    cash_received = float(input("Ingrese el monto: "))
    cash = Cash(total, "Efectivo", cash_received)
    cambio = cash.pay()
    if cambio:
        print("El cambio es de $", cambio)
    else:
        print("El monto recibido no es suficiente")

if metodo == 2:
    total = orden1.calcularTotal()
    card_number = input("Ingrese el número de la tarjeta: ")
    expiration_date = input("Ingrese la fecha de expiración: ")
    cvv = input("Ingrese el código de seguridad: ")
    card = Card(total, "Tarjeta", card_number, expiration_date, cvv)
    if card.pay():
        print("Pago exitoso")
    else:
        print("Pago rechazado")

elif metodo != 1 and metodo != 2:
    print("Método de pago no válido")
```
