LAUTARO MIRABAL 5°14


trabajo integrador 

using System;
using System.Collections.Generic;

public class Producto
{
    public string Nombre { get; private set; }
    public decimal Precio { get; private set; }
    public int Stock { get; private set; }

    public Producto(string nombre, decimal precio, int stock)
    {
        Nombre = nombre;
        Precio = precio;
        Stock = stock;
    }

    public bool DescontarStock(int cantidad)
    {
        if (cantidad > 0 && cantidad <= Stock)
        {
            Stock = Stock - cantidad;
            return true;
        }

        return false;
    }
}


public class Pedido
{
    private List<Producto> _productos = new List<Producto>();

    public string Estado { get; private set; } = "Pendiente";

    public void AgregarProducto(Producto p, int cant)
    {
        if (p.DescontarStock(cant))
        {
            for (int i = 0; i < cant; i++)
            {
                _productos.Add(p);
            }

            Console.WriteLine("Producto agregado correctamente.");
        }
        else
        {
            Console.WriteLine("No hay stock suficiente.");
        }
    }

    public decimal CalcularTotal(decimal impuesto)
    {
        decimal subtotal = 0;

        foreach (Producto producto in _productos)
        {
            subtotal = subtotal + producto.Precio;
        }

        decimal total = subtotal + (subtotal * impuesto);

        return total;
    }

    public void Pagar()
    {
        if (_productos.Count > 0)
        {
            Estado = "Completado";
        }
    }

    public void MostrarProductos()
    {
        if (_productos.Count == 0)
        {
            Console.WriteLine("El pedido esta vacio.");
            return;
        }

        Console.WriteLine("Productos del pedido:");

        foreach (Producto producto in _productos)
        {
            Console.WriteLine("- " + producto.Nombre + " $" + producto.Precio);
        }
    }
}


class Program
{
    static List<Producto> inventario = new List<Producto>();

    static void Main(string[] args)
    {
        inventario.Add(new Producto("Pan", 1000, 10));
        inventario.Add(new Producto("Leche", 1500, 5));
        inventario.Add(new Producto("Galletitas", 1200, 3));
        inventario.Add(new Producto("Cafe", 2500, 4));

        Pedido pedido = new Pedido();

        bool ejecutando = true;

        while (ejecutando)
        {
            Console.WriteLine();
            Console.WriteLine("===== MENU PRINCIPAL =====");
            Console.WriteLine("1. Mostrar inventario");
            Console.WriteLine("2. Agregar producto");
            Console.WriteLine("3. Mostrar resumen");
            Console.WriteLine("4. Pagar pedido");
            Console.WriteLine("5. Salir");
            Console.Write("Seleccione una opcion: ");

            int opcion;

            if (int.TryParse(Console.ReadLine(), out opcion))
            {
                switch (opcion)
                {
                    case 1:
                        MostrarInventario();
                        break;

                    case 2:
                        AgregarProductoAlPedido(pedido);
                        break;

                    case 3:
                        MostrarResumen(pedido);
                        break;

                    case 4:
                        pedido.Pagar();
                        Console.WriteLine("Pedido pagado correctamente.");
                        break;

                    case 5:
                        ejecutando = false;
                        Console.WriteLine("Programa finalizado.");
                        break;

                    default:
                        Console.WriteLine("Opcion no valida.");
                        break;
                }
            }
            else
            {
                Console.WriteLine("Debe ingresar un numero.");
            }
        }
    }

    static void MostrarInventario()
    {
        Console.WriteLine();
        Console.WriteLine("===== INVENTARIO =====");

        for (int i = 0; i < inventario.Count; i++)
        {
            Console.WriteLine(
                (i + 1) + ". " +
                inventario[i].Nombre +
                " - $" + inventario[i].Precio +
                " - Stock: " + inventario[i].Stock
            );
        }
    }

    static void AgregarProductoAlPedido(Pedido pedido)
    {
        MostrarInventario();

        Console.Write("Seleccione el numero del producto: ");

        int opcion;

        if (!int.TryParse(Console.ReadLine(), out opcion))
        {
            Console.WriteLine("Debe ingresar un numero.");
            return;
        }

        if (opcion < 1 || opcion > inventario.Count)
        {
            Console.WriteLine("Producto no valido.");
            return;
        }

        Producto producto = inventario[opcion - 1];

        Console.Write("Ingrese la cantidad: ");

        int cantidad;

        if (!int.TryParse(Console.ReadLine(), out cantidad))
        {
            Console.WriteLine("Debe ingresar un numero.");
            return;
        }

        pedido.AgregarProducto(producto, cantidad);
    }

    static void MostrarResumen(Pedido pedido)
    {
        Console.WriteLine();
        Console.WriteLine("===== RESUMEN DEL PEDIDO =====");

        pedido.MostrarProductos();

        decimal impuesto = 0.21m;
        decimal total = pedido.CalcularTotal(impuesto);

        Console.WriteLine();
        Console.WriteLine("Impuesto: 21%");
        Console.WriteLine("Total: $" + total.ToString("F2"));
        Console.WriteLine("Estado: " + pedido.Estado);
    }
}
