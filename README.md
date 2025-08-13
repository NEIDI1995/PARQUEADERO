# PARQUEADERO
Proyecto en java que gestiona un parqueadero usando herencia y polimorfismo


ProyectoParqueadero/
├── src/
│   └── parqueadero/
│       ├── Vehiculo.java
│       ├── Automovil.java
│       ├── Motocicleta.java
│       ├── Camion.java
│       ├── Parqueadero.java
│       └── Main.java
└── README.md


package parqueadero;

import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.*;

abstract class Vehiculo {
    protected String placa, marca, modelo;
    protected LocalDateTime horaEntrada;

    public Vehiculo(String placa, String marca, String modelo, LocalDateTime horaEntrada) {
        this.placa = placa;
        this.marca = marca;
        this.modelo = modelo;
        this.horaEntrada = horaEntrada;
    }

    public String getPlaca() { return placa; }
    public LocalDateTime getHoraEntrada() { return horaEntrada; }
    public abstract double calcularTarifa(long horas);
    public abstract String getDescripcion();
}

class Automovil extends Vehiculo {
    private String tipoCombustible;

    public Automovil(String placa, String marca, String modelo, LocalDateTime horaEntrada, String tipoCombustible) {
        super(placa, marca, modelo, horaEntrada);
        this.tipoCombustible = tipoCombustible;
    }

    public double calcularTarifa(long horas) { return horas * 3000; }

    public String getDescripcion() {
        return "Automóvil | " + marca + " " + modelo + " | Combustible: " + tipoCombustible;
    }
}

class Motocicleta extends Vehiculo {
    private int cilindraje;

    public Motocicleta(String placa, String marca, String modelo, LocalDateTime horaEntrada, int cilindraje) {
        super(placa, marca, modelo, horaEntrada);
        this.cilindraje = cilindraje;
    }

    public double calcularTarifa(long horas) { return horas * 1500; }

    public String getDescripcion() {
        return "Motocicleta | " + marca + " " + modelo + " | " + cilindraje + "cc";
    }
}

class Camion extends Vehiculo {
    private double capacidadCarga;

    public Camion(String placa, String marca, String modelo, LocalDateTime horaEntrada, double capacidadCarga) {
        super(placa, marca, modelo, horaEntrada);
        this.capacidadCarga = capacidadCarga;
    }

    public double calcularTarifa(long horas) { return horas * 7000; }

    public String getDescripcion() {
        return "Camión | " + marca + " " + modelo + " | Carga: " + capacidadCarga + "t";
    }
}

class Parqueadero {
    private List<Vehiculo> vehiculos = new ArrayList<>();

    public void registrarEntrada(Vehiculo v) {
        vehiculos.add(v);
        System.out.println("Vehículo registrado correctamente.");
    }

    public void registrarSalida(String placa) {
        Vehiculo v = vehiculos.stream()
                .filter(x -> x.getPlaca().equalsIgnoreCase(placa))
                .findFirst().orElse(null);

        if (v != null) {
            LocalDateTime salida = LocalDateTime.now();
            long minutos = Duration.between(v.getHoraEntrada(), salida).toMinutes();
            long horas = (minutos + 59) / 60;
            double tarifa = v.calcularTarifa(horas);

            DateTimeFormatter formato = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");
            System.out.println("\n--- SALIDA ---");
            System.out.println("Vehículo: " + v.getDescripcion());
            System.out.println("Placa: " + v.getPlaca());
            System.out.println("Entrada: " + v.getHoraEntrada().format(formato));
            System.out.println("Salida: " + salida.format(formato));
            System.out.println("Tiempo: " + horas + " hora(s)");
            System.out.println("Total: $" + tarifa);
            vehiculos.remove(v);
        } else {
            System.out.println("No se encontró un vehículo con esa placa.");
        }
    }

    public void mostrarVehiculos() {
        if (vehiculos.isEmpty()) {
            System.out.println("No hay vehículos en el parqueadero.");
        } else {
            System.out.println("--- VEHÍCULOS ACTUALES ---");
            vehiculos.forEach(v -> System.out.println(v.getPlaca() + " | " + v.getDescripcion()));
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Parqueadero parqueadero = new Parqueadero();

        while (true) {
            System.out.println("\n--- PARQUEADERO ---");
            System.out.println("1. Registrar entrada");
            System.out.println("2. Registrar salida");
            System.out.println("3. Ver vehículos");
            System.out.println("4. Salir");
            System.out.print("Opción: ");
            int op = sc.nextInt(); sc.nextLine();

            switch (op) {
                case 1 -> {
                    System.out.print("Tipo (automovil, motocicleta, camion): ");
                    String tipo = sc.nextLine().toLowerCase();
                    System.out.print("Placa: ");
                    String placa = sc.nextLine();
                    System.out.print("Marca: ");
                    String marca = sc.nextLine();
                    System.out.print("Modelo: ");
                    String modelo = sc.nextLine();
                    LocalDateTime entrada = LocalDateTime.now();

                    Vehiculo v = null;
                    if (tipo.equals("automovil")) {
                        System.out.print("Tipo de combustible: ");
                        String combustible = sc.nextLine();
                        v = new Automovil(placa, marca, modelo, entrada, combustible);
                    } else if (tipo.equals("motocicleta")) {
                        System.out.print("Cilindraje: ");
                        int cilindraje = sc.nextInt(); sc.nextLine();
                        v = new Motocicleta(placa, marca, modelo, entrada, cilindraje);
                    } else if (tipo.equals("camion")) {
                        System.out.print("Capacidad de carga (toneladas): ");
                        double carga = sc.nextDouble(); sc.nextLine();
                        v = new Camion(placa, marca, modelo, entrada, carga);
                    }

                    if (v != null) {
                        parqueadero.registrarEntrada(v);
                    } else {
                        System.out.println("Tipo no válido.");
                    }
                }
                case 2 -> {
                    System.out.print("Placa del vehículo: ");
                    String placa = sc.nextLine();
                    parqueadero.registrarSalida(placa);
                }
                case 3 -> parqueadero.mostrarVehiculos();
                case 4 -> {
                    System.out.println("Programa finalizado.");
                    return;
                }
                default -> System.out.println("Opción no válida.");
            }
        }
    }
}
