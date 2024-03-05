# ankit38
this is a program where perform CRUD function are performed with the help of JDBC or DAO data Access Object
import java.sql.*;
import java.util.Scanner;

public class UserDAO {

    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/mydatabase";
    private static final String USERNAME = "your_username";
    private static final String PASSWORD = "your_password";

    public static void main(String[] args) {
        try (Connection connection = DriverManager.getConnection(JDBC_URL, USERNAME, PASSWORD);
             Scanner scanner = new Scanner(System.in)) {

            while (true) {
                System.out.println("Choose operation:");
                System.out.println("1. Create");
                System.out.println("2. Read");
                System.out.println("3. Update");
                System.out.println("4. Delete");
                System.out.println("5. Exit");

                int choice = scanner.nextInt();
                scanner.nextLine(); // Consume newline

                switch (choice) {
                    case 1:
                        createUser(connection, scanner);
                        break;
                    case 2:
                        readUsers(connection);
                        break;
                    case 3:
                        updateUser(connection, scanner);
                        break;
                    case 4:
                        deleteUser(connection, scanner);
                        break;
                    case 5:
                        System.out.println("Exiting program...");
                        return;
                    default:
                        System.out.println("Invalid choice. Please choose a number from 1 to 5.");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static void createUser(Connection connection, Scanner scanner) throws SQLException {
        System.out.println("Enter name:");
        String name = scanner.nextLine();
        System.out.println("Enter email:");
        String email = scanner.nextLine();

        String insertQuery = "INSERT INTO users (name, email) VALUES (?, ?)";
        try (PreparedStatement insertStatement = connection.prepareStatement(insertQuery)) {
            insertStatement.setString(1, name);
            insertStatement.setString(2, email);
            insertStatement.executeUpdate();
        }
        System.out.println("User created successfully.");
    }

    private static void readUsers(Connection connection) throws SQLException {
        String selectQuery = "SELECT * FROM users";
        try (Statement statement = connection.createStatement();
             ResultSet resultSet = statement.executeQuery(selectQuery)) {

            System.out.println("ID\tName\tEmail");
            while (resultSet.next()) {
                System.out.println(resultSet.getInt("id") + "\t" +
                        resultSet.getString("name") + "\t" +
                        resultSet.getString("email"));
            }
        }
    }

    private static void updateUser(Connection connection, Scanner scanner) throws SQLException {
        System.out.println("Enter ID of user to update:");
        int id = scanner.nextInt();
        scanner.nextLine(); // Consume newline
        System.out.println("Enter new name:");
        String newName = scanner.nextLine();
        System.out.println("Enter new email:");
        String newEmail = scanner.nextLine();

        String updateQuery = "UPDATE users SET name = ?, email = ? WHERE id = ?";
        try (PreparedStatement updateStatement = connection.prepareStatement(updateQuery)) {
            updateStatement.setString(1, newName);
            updateStatement.setString(2, newEmail);
            updateStatement.setInt(3, id);
            updateStatement.executeUpdate();
        }
        System.out.println("User updated successfully.");
    }

    private static void deleteUser(Connection connection, Scanner scanner) throws SQLException {
        System.out.println("Enter ID of user to delete:");
        int id = scanner.nextInt();
        scanner.nextLine(); // Consume newline

        String deleteQuery = "DELETE FROM users WHERE id = ?";
        try (PreparedStatement deleteStatement = connection.prepareStatement(deleteQuery)) {
            deleteStatement.setInt(1, id);
            deleteStatement.executeUpdate();
        }
        System.out.println("User deleted successfully.");
    }
}
