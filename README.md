# Reto-07
Uso de FIFO queues, tuplas y diccionarios en la clase restaurante.

## FIFO Queue para manipular múltiples órdenes
```python
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
## Ejemplo de uso: 

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
