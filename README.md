
```java
import java.util.*;

// Customer class
class Customer {
    private String taxNumber;
    private String iban;
    private String contactInfo;
    private String category;
    private boolean isActive;

    // Getter and Setter methods
    // ...
}

// Customer Grouping
enum CustomerType {
    RETAIL, WHOLESALE
}

class CustomerGroup {
    private CustomerType type;
    private String region;

    // Constructor, Getter and Setter methods
    // ...
}

// Order Status Enum
enum OrderStatus {
    QUOTE, PENDING_APPROVAL, APPROVED, COMPLETED, CANCELLED
}

// Sale Order Class
class SaleOrder {
    private List<OrderItem> items;
    private OrderStatus status;
    private String notes;
    private Currency currency;
    private List<String> attachedFiles;

    public SaleOrder() {
        items = new ArrayList<>();
        attachedFiles = new ArrayList<>();
    }

    public void addItem(OrderItem item) {
        items.add(item);
    }

    // Other methods...
    // ...
}

class OrderItem {
    private String productName;
    private int quantity;
    private double unitPrice;

    // Constructor, Getter and Setter methods
    // ...
}

// Stock Management
class StockManager {
    private Map<String, Integer> stock;

    public StockManager() {
        stock = new HashMap<>();
    }

    public boolean isProductAvailable(String productName, int quantity) {
        return stock.getOrDefault(productName, 0) >= quantity;
    }

    public void reduceStock(String productName, int quantity) {
        if (isProductAvailable(productName, quantity)) {
            stock.put(productName, stock.get(productName) - quantity);
        }
    }

    // Other methods...
    // ...
}

// Invoice Management
class Invoice {
    private static int invoiceCounter = 0;
    private int invoiceNumber;
    private boolean isVATIncluded;

    public Invoice(boolean isVATIncluded) {
        this.invoiceNumber = ++invoiceCounter;
        this.isVATIncluded = isVATIncluded;
    }

    // Invoice generation methods including PDF export
    // ...
}

// Reporting
class ReportGenerator {
    public void generateSalesReportByCustomer(Customer customer) {
        // Implementation
    }

    public void generateSalesAnalysisByProduct(String productName) {
        // Implementation
    }

    public void generateMonthlyYearlySalesTrend() {
        // Implementation
    }

    public void exportToExcel() {
        // Implementation
    }

    public void exportToPDF() {
        // Implementation
    }

    // ...
}

// Authorization
enum UserRole {
    SALES_PERSON, MANAGER, ACCOUNTANT
}

class User {
    private String username;
    private UserRole role;

    public User(String username, UserRole role) {
        this.username = username;
        this.role = role;
    }

    // Other methods...
    // ...
}

// Multi-currency and Language Support
class CurrencyConverter {
    private Map<String, Double> exchangeRates;

    public CurrencyConverter() {
        exchangeRates = new HashMap<>();
    }

    public double convertCurrency(double amount, Currency from, Currency to) {
        // Implementation of currency conversion
        return amount * (exchangeRates.get(to.name()) / exchangeRates.get(from.name()));
    }

    public void updateExchangeRates() {
        // Fetch and update exchange rates from TCMB API
    }

    // ...
}

// Main class
public class ERPSystem {
    public static void main(String[] args) {
        // System implementation
        // Initialize components
        StockManager stockManager = new StockManager();
        Invoice invoice = new Invoice(true);
        ReportGenerator reportGenerator = new ReportGenerator();

        // Sample operational flow
        // ...
    }
}
`````java
import java.time.LocalDate;
import java.time.temporal.ChronoUnit;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.stream.Collectors;

class Invoice {
    private LocalDate invoiceDate;
    private LocalDate paymentDate;
    private String paymentType; // "peşin", "vadeli", "çek", "EFT"

    // Constructor, getters, and setters omitted for brevity

    public long calculatePaymentDuration() {
        return ChronoUnit.DAYS.between(invoiceDate, paymentDate);
    }
}

class PaymentAnalysis {
    public double calculateAveragePaymentDuration(List<Invoice> invoices) {
        return invoices.stream()
                       .mapToLong(Invoice::calculatePaymentDuration)
                       .average()
                       .orElse(0);
    }

    public double calculateLatePaymentRate(List<Invoice> invoices) {
        long latePayments = invoices.stream()
                                    .filter(invoice -> invoice.calculatePaymentDuration() > 30) // Assuming 30 days is late
                                    .count();
        return (double) latePayments / invoices.size() * 100;
    }

    public long findLongestPaymentDuration(List<Invoice> invoices) {
        return invoices.stream()
                       .mapToLong(Invoice::calculatePaymentDuration)
                       .max()
                       .orElse(0);
    }

    public void alertForDueDates(List<Invoice> invoices) {
        LocalDate now = LocalDate.now();
        invoices.stream()
                .filter(invoice -> invoice.calculatePaymentDuration() > 30 && invoice.getPaymentDate().isAfter(now))
                .forEach(invoice -> {
                    System.out.println("Alert: Invoice from " + invoiceDate + " is overdue.");
                });
    }
}

class Order {
    private LocalDate orderDate;
    private double totalAmount;
    private Set<String> productTypes;

    // Constructor, getters, and setters omitted for brevity
}

class Customer {
    private List<Order> orderHistory;

    // Constructor, getters, and setters omitted for brevity

    public double calculateMonthlyOrderFrequency() {
        long monthsBetween = ChronoUnit.MONTHS.between(
                orderHistory.get(0).getOrderDate(), 
                orderHistory.get(orderHistory.size() - 1).getOrderDate()) + 1;
        return (double) orderHistory.size() / monthsBetween;
    }

    public boolean isActiveCustomer() {
        LocalDate lastOrderDate = orderHistory.stream()
                                              .map(Order::getOrderDate)
                                              .max(LocalDate::compareTo)
                                              .orElse(LocalDate.of(1970, 1, 1));
        return ChronoUnit.MONTHS.between(lastOrderDate, LocalDate.now()) <= 6;
    }

    public int getTotalOrderCount() {
        return orderHistory.size();
    }

    public double getTotalOrderAmount() {
        return orderHistory.stream()
                           .mapToDouble(Order::getTotalAmount)
                           .sum();
    }

    public int countUniqueProductTypes() {
        return orderHistory.stream()
                           .flatMap(order -> order.getProductTypes().stream())
                           .collect(Collectors.toSet())
                           .size();
    }
}
```
