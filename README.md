import java.io.*;
import java.util.*;

class Employee {
    private String name;
    private int age;
    private int id;
    private double salary;

    public Employee(String name, int age, int id, double salary) {
        this.name = name;
        this.age = age;
        this.id = id;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public int getId() {
        return id;
    }

    public double getSalary() {
        return salary;
    }
}

public class EmployeeManagementSystem {
    private static final String FILE_NAME = "employees.txt";
    private static Map<String, List<Employee>> userEmployees = new HashMap<>();

    public static void main(String[] args) {
        loadEmployeesFromFile();

        System.out.println("\t\t***Employee Management System***");
        Scanner scanner = new Scanner(System.in);

        int choice;
        String username;
        do {
            System.out.print("\nEnter username: ");
            username = scanner.nextLine();
            if (!userEmployees.containsKey(username)) {
                userEmployees.put(username, new ArrayList<>());
            }

            System.out.println("\nMenu:");
            System.out.println("1. Add Employee");
            System.out.println("2. Delete Employee");
            System.out.println("3. View Employees");
            System.out.println("4. Retrieve Employee");
            System.out.println("5. Exit");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline character

            switch (choice) {
                case 1:
                    addEmployee(scanner, username);
                    break;
                case 2:
                    deleteEmployee(scanner, username);
                    break;
                case 3:
                    viewEmployees(username);
                    break;
                case 4:
                    retrieveEmployee(scanner, username);
                    break;
                case 5:
                    System.out.println("Exiting...");
                    break;
                default:
                    System.out.println("Invalid choice. Please enter a number from 1 to 5.");
            }
        } while (choice != 5);

        scanner.close();
    }

    public static void loadEmployeesFromFile() {
        try (Scanner fileScanner = new Scanner(new File(FILE_NAME))) {
            while (fileScanner.hasNextLine()) {
                String[] employeeData = fileScanner.nextLine().split(",");
                String username = employeeData[0];
                String name = employeeData[1];
                int age = Integer.parseInt(employeeData[2]);
                int id = Integer.parseInt(employeeData[3]);
                double salary = Double.parseDouble(employeeData[4]);
                Employee employee = new Employee(name, age, id, salary);
                List<Employee> employees = userEmployees.getOrDefault(username, new ArrayList<>());
                employees.add(employee);
                userEmployees.put(username, employees);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + FILE_NAME);
        }
    }

    public static void addEmployee(Scanner scanner, String username) {
        System.out.println("\nEnter details for the new Employee:");
        System.out.print("Name: ");
        String name = scanner.nextLine();

        System.out.print("Age: ");
        int age = scanner.nextInt();

        System.out.print("ID: ");
        int id = scanner.nextInt();

        System.out.print("Salary: ");
        double salary = scanner.nextDouble();

        Employee employee = new Employee(name, age, id, salary);
        List<Employee> employees = userEmployees.get(username);
        if (employees == null) {
            employees = new ArrayList<>();
        }
        employees.add(employee);
        userEmployees.put(username, employees);

        System.out.println("\nNew employee added successfully:");
        System.out.println("Name: " + name);
        System.out.println("Age: " + age);
        System.out.println("ID: " + id);
        System.out.println("Salary: " + salary);

        saveEmployeesToFile();
    }

    public static void deleteEmployee(Scanner scanner, String username) {
        System.out.print("\nEnter the ID of the employee to delete: ");
        int idToDelete = scanner.nextInt();

        List<Employee> employees = userEmployees.get(username);
        if (employees == null || employees.isEmpty()) {
            System.out.println("No employees found for this user.");
            return;
        }

        Employee deletedEmployee = null;
        Iterator<Employee> iterator = employees.iterator();
        while (iterator.hasNext()) {
            Employee employee = iterator.next();
            if (employee.getId() == idToDelete) {
                deletedEmployee = employee;
                iterator.remove();
                System.out.println("Employee with ID " + idToDelete + " deleted successfully.");
                break;
            }
        }
        if (deletedEmployee != null) {
            saveEmployeesToFile();
            System.out.println("Retrieved deleted employee: " + deletedEmployee.getName());
        } else {
            System.out.println("Employee with ID " + idToDelete + " not found.");
        }
    }

    public static void viewEmployees(String username) {
        List<Employee> employees = userEmployees.get(username);
        if (employees == null || employees.isEmpty()) {
            System.out.println("No employees found for this user.");
            return;
        }

        System.out.println("\nEmployee Details:");
        for (Employee employee : employees) {
            System.out.println("Name: " + employee.getName());
            System.out.println("Age: " + employee.getAge());
            System.out.println("ID: " + employee.getId());
            System.out.println("Salary: " + employee.getSalary());
            System.out.println();
        }
    }

    public static void retrieveEmployee(Scanner scanner, String username) {
        deleteEmployee(scanner, username); // Calls deleteEmployee method to retrieve
    }

    public static void saveEmployeesToFile() {
        try (PrintWriter writer = new PrintWriter(new File(FILE_NAME))) {
            for (Map.Entry<String, List<Employee>> entry : userEmployees.entrySet()) {
                String username = entry.getKey();
                List<Employee> employees = entry.getValue();
                for (Employee employee : employees) {
                    writer.println(username + "," + employee.getName() + "," + employee.getAge() + "," + employee.getId() + "," + employee.getSalary());
                }
            }
            System.out.println("\nEmployee details have been saved to " + FILE_NAME);
        } catch (FileNotFoundException e) {
            System.out.println("Error: Could not write to file.");
        }
    }
}
