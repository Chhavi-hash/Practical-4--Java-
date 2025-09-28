import java.io.*;
import java.util.ArrayList;
import java.util.InputMismatchException;
import java.util.Scanner;

// Student class
class Student implements Serializable {
    private static final long serialVersionUID = 1L;
    int studentID;
    String name;
    String grade;

    public Student(int studentID, String name, String grade) {
        this.studentID = studentID;
        this.name = name;
        this.grade = grade;
    }

    @Override
    public String toString() {
        return "StudentID: " + studentID + ", Name: " + name + ", Grade: " + grade;
    }
}

// Employee class
class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    String name;
    int id;
    String designation;
    double salary;

    public Employee(String name, int id, String designation, double salary) {
        this.name = name;
        this.id = id;
        this.designation = designation;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "ID: " + id + ", Name: " + name + ", Designation: " + designation + ", Salary: " + salary;
    }
}

public class NimbusApplication {

    // Part a: Sum of integers using autoboxing/unboxing
    public static void sumIntegers(Scanner sc) {
        ArrayList<Integer> numbers = new ArrayList<>();
        System.out.println("Enter integers separated by space:");
        String input = sc.nextLine();
        String[] parts = input.split("\\s+");

        for (String s : parts) {
            try {
                int num = Integer.parseInt(s);
                numbers.add(num); // Autoboxing
            } catch (NumberFormatException e) {
                System.out.println("Invalid number skipped: " + s);
            }
        }

        int sum = 0;
        for (Integer n : numbers) {
            sum += n; // Unboxing
        }

        System.out.println("Sum of entered integers: " + sum);
    }

    // Part b: Student serialization/deserialization
    public static void studentSerialization(Scanner sc) {
        try {
            System.out.print("Enter Student ID: ");
            int id = sc.nextInt();
            sc.nextLine(); // consume newline
            System.out.print("Enter Student Name: ");
            String name = sc.nextLine();
            System.out.print("Enter Grade: ");
            String grade = sc.nextLine();

            Student student = new Student(id, name, grade);

            // Serialize student
            try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("student.dat"))) {
                oos.writeObject(student);
                System.out.println("Student object serialized successfully.");
            }

            // Deserialize student
            try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("student.dat"))) {
                Student deserializedStudent = (Student) ois.readObject();
                System.out.println("Deserialized Student: " + deserializedStudent);
            }

        } catch (InputMismatchException e) {
            System.out.println("Invalid input. Please enter correct data types.");
            sc.nextLine(); // clear buffer
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    // Part c: Menu-based Employee Management System
    public static void employeeManagement(Scanner sc) {
        String fileName = "employees.dat";
        ArrayList<Employee> employees = new ArrayList<>();

        // Load existing employees
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(fileName))) {
            employees = (ArrayList<Employee>) ois.readObject();
        } catch (FileNotFoundException | EOFException e) {
            // First run or empty file, ignore
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }

        boolean exit = false;
        while (!exit) {
            System.out.println("\nEmployee Management System Menu:");
            System.out.println("1. Add Employee");
            System.out.println("2. Display All Employees");
            System.out.println("3. Exit");
            System.out.print("Enter choice: ");

            int choice;
            try {
                choice = sc.nextInt();
                sc.nextLine(); // consume newline
            } catch (InputMismatchException e) {
                System.out.println("Invalid input. Please enter a number.");
                sc.nextLine();
                continue;
            }

            switch (choice) {
                case 1:
                    try {
                        System.out.print("Enter Employee ID: ");
                        int id = sc.nextInt();
                        sc.nextLine();
                        System.out.print("Enter Name: ");
                        String name = sc.nextLine();
                        System.out.print("Enter Designation: ");
                        String designation = sc.nextLine();
                        System.out.print("Enter Salary: ");
                        double salary = sc.nextDouble();
                        sc.nextLine();

                        Employee emp = new Employee(name, id, designation, salary);
                        employees.add(emp);

                        // Save employees to file
                        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(fileName))) {
                            oos.writeObject(employees);
                        }

                        System.out.println("Employee added successfully.");
                    } catch (InputMismatchException e) {
                        System.out.println("Invalid input. Employee not added.");
                        sc.nextLine();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                    break;

                case 2:
                    if (employees.isEmpty()) {
                        System.out.println("No employees found.");
                    } else {
                        System.out.println("Employee List:");
                        for (Employee e : employees) {
                            System.out.println(e);
                        }
                    }
                    break;

                case 3:
                    exit = true;
                    System.out.println("Exiting Employee Management System.");
                    break;

                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("=== Nimbus Java Program ===");
        boolean exitProgram = false;

        while (!exitProgram) {
            System.out.println("\nSelect Part to Execute:");
            System.out.println("1. Sum of Integers (Autoboxing/Unboxing)");
            System.out.println("2. Student Serialization/Deserialization");
            System.out.println("3. Employee Management System (File Handling)");
            System.out.println("4. Exit");
            System.out.print("Enter choice: ");

            int choice;
            try {
                choice = sc.nextInt();
                sc.nextLine();
            } catch (InputMismatchException e) {
                System.out.println("Invalid input. Please enter a number.");
                sc.nextLine();
                continue;
            }

            switch (choice) {
                case 1:
                    sumIntegers(sc);
                    break;
                case 2:
                    studentSerialization(sc);
                    break;
                case 3:
                    employeeManagement(sc);
                    break;
                case 4:
                    exitProgram = true;
                    System.out.println("Exiting Nimbus Application.");
                    break;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }

        sc.close();
    }
}
