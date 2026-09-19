
import java.util.Scanner;

public class InventorySystem {
    // डेटाबेस कॉन्फ़िगरेशन डिटेल्स
    private static final String URL = "jdbc:mysql://localhost:3306/inventory_db";
    private static final String USER = "root";
    private static final String PASSWORD = "password";

    public static void main(String[] args) {
        try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD)) {
            System.out.println("--- Connected to Warehouse Inventory Database ---");
            Scanner scanner = new Scanner(System.in);
            
            while (true) {
                System.out.println("\n1. Add Product\n2. View Stock\n3. Update Dispatch\n4. Exit");
                System.out.print("Choose an option: ");
                int choice = scanner.nextInt();
                
                if (choice == 4) break;
                
                switch (choice) {
                    case 1:
                        System.out.print("Enter Product Name: ");
                        String name = scanner.next();
                        System.out.print("Enter Quantity: ");
                        int qty = scanner.nextInt();
                        
                        String query = "INSERT INTO products (name, quantity) VALUES (?, ?)";
                        PreparedStatement pstmt = conn.prepareStatement(query);
                        pstmt.setString(1, name);
                        pstmt.setInt(2, qty);
                        pstmt.executeUpdate();
                        System.out.println("Product added successfully!");
                        break;
                        
                    case 2:
                        Statement stmt = conn.createStatement();
                        ResultSet rs = stmt.executeQuery("SELECT * FROM products");
                        System.out.println("\n--- Current Inventory Stock ---");
                        while (rs.next()) {
                            System.out.println("ID: " + rs.getInt("id") + " | Name: " + rs.getString("name") + " | Qty: " + rs.getInt("quantity"));
                        }
                        break;
                        
                    case 3:
                        System.out.print("Enter Product ID to dispatch: ");
                        int id = scanner.nextInt();
                        System.out.print("Enter quantity to dispatch: ");
                        int dQty = scanner.nextInt();
                        
                        String updateQuery = "UPDATE products SET quantity = quantity - ? WHERE id = ?";
                        PreparedStatement uPstmt = conn.prepareStatement(updateQuery);
                        uPstmt.setInt(1, dQty);
                        uPstmt.setInt(2, id);
                        uPstmt.executeUpdate();
                        System.out.println("Order dispatched and stock updated!");
                        break;
                }
            }
        } catch (SQLException e) {
            System.out.println("Database connection error: " + e.getMessage());
        }
    }
}
