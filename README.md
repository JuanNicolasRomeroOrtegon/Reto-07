# Reto-07
Uso de FIFO queues, tuplas y diccionarios en la clase restaurante.

## FIFO Queue para manipular múltiples órdenes
```python
from queue import Queue

class OrderQueue: 
    def __init__(self) -> None:
        self.orders = Queue()
    
    def add_order(self, order: Order) -> None:
        self.orders.put(order)
        print(f"Order added for: {order.customer_name}")

    def process_order(self) -> None: 
        if not self.orders.empty():
            order_processed = self.orders.get()
            print(f"\nOrder processed for: {order_processed.customer_name}")
            order_processed.bill()
        else:
            print("There are no orders left")

    def pending_orders(self) -> int:
        return self.orders.qsize()
    
    def process_all_orders(self) -> None:
        while not self.orders.empty():
            self.process_order()
```
Se añaden y procesan las órdenes de los clientes.
### Ejemplo de uso: 

```python
order1 = Order("Juan")
order1.add_item(MainCourse("Burger", 10.0, "regular", "beef", "fries"))
order1.add_item(Beverage("Coke", 3.0, "large"))
order2 = Order("Ana")
order2.add_item(Salad("Caesar Salad", 8.0, "large", "ranch", True))
order2.add_item(Dessert("Chocolate Cake", 5.0, True, True, True))

order_queue = OrderQueue()

order_queue.add_order(order1)
order_queue.add_order(order2)

order_queue.process_all()
```

## Named Tuple
```python
MainCourseTuple = namedtuple(
    "MainCourseTuple",
    ["name", "price", "size", "protein", "carbs", "is_vegetarian", 
    "walnut_allergy", "cereal_allergy", "egg_allergy"]
)
```
Implementación de la named tuple.

```python
plato1 = MainCourseTuple(
    name="Grilled Chicken",
    price=15.0,
    size="regular",
    protein="Chicken",
    carbs="Rice",
    is_vegetarian=False,
    walnut_allergy=False,
    cereal_allergy=False,
    egg_allergy=False
)

plato2 = MainCourseTuple(
    name="Vegetarian Lasagna",
    price=13.5,
    size="large",
    protein="Tofu",
    carbs="Pasta",
    is_vegetarian=True,
    walnut_allergy=False,
    cereal_allergy=True,
    egg_allergy=False
)

plato3 = MainCourseTuple(
    name="Beef Burger",
    price=12.0,
    size="small",
    protein="Beef",
    carbs="Fries",
    is_vegetarian=False,
    walnut_allergy=False,
    cereal_allergy=False,
    egg_allergy=True
)
```
Instancias de la implementación.


## Menú implementado con JSON

```python
class MenuManager:

    def __init__(self, filename: str = "menu.json") -> None:
        self.filename = filename
        self.menu = self._load_menu()

    def _load_menu(self) -> dict:
   
        try:
            with open(self.filename, 'r') as f:
                return json.load(f)
        except FileNotFoundError:
            # Empezar con un menu vacío
            return {}

    def _save_menu(self) -> None:
        with open(self.filename, 'w') as f:
            json.dump(self.menu, f, indent=4)

    def add_item(self, category: str, item_name: str, item_details: dict) -> None:
        if category not in self.menu:
            self.menu[category] = {}
        if item_name in self.menu[category]:
            print(f"Error: Item '{item_name}' already exists in '{category}'.")
            return
        self.menu[category][item_name] = item_details
        self._save_menu()
        print(f"Successfully added '{item_name}' to the '{category}' menu.")

    def update_item(self, category: str, item_name: str, updated_details: dict) -> None:
        if category in self.menu and item_name in self.menu[category]:
            self.menu[category][item_name].update(updated_details)
            self._save_menu()
            print(f"Successfully updated '{item_name}' in the '{category}' menu.")
        else:
            print(f"Error: Item '{item_name}' not found in '{category}'.")

    def delete_item(self, category: str, item_name: str) -> None:
        if category in self.menu and item_name in self.menu[category]:
            del self.menu[category][item_name]
            if not self.menu[category]:
                del self.menu[category]
            self._save_menu()
            print(f"Successfully deleted '{item_name}' from '{category}'.")
        else:
            print(f"Error: Item '{item_name}' not found in '{category}'.")
```

###

```python
class Order:
    def __init__(self, customer_name: str, menu_manager: MenuManager) -> None:
        self.customer_name: str = customer_name
        self.items: list[MenuItem] = []
        self.discount: float = 0
        self.tax_rate: float = 0.08
        self.menu_manager = menu_manager

    def add_to_menu(self, category: str, item_name: str, item_details: dict):
        self.menu_manager.add_item(category, item_name, item_details)

    def update_in_menu(self, category: str, item_name: str, updated_details: dict):
        self.menu_manager.update_item(category, item_name, updated_details)

    def delete_from_menu(self, category: str, item_name: str):
        self.menu_manager.delete_item(category, item_name)

    def add_item(self, menu_item: MenuItem, quantity: int = 1) -> None:
        for _ in range(quantity):
            if hasattr(menu_item, 'calculate_price'):
                menu_item.calculate_price()
            self.items.append(menu_item)
```
### Ejemplo de uso: 
