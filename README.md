# Bank-Operations-Management
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_ACCOUNTS 100
#define MAX_LOANS 50

// Structure to store account information
struct Account {
    int accountNumber;
    char name[100];
    float balance;
};

// Structure to store loan information
struct Loan {
    int accountNumber;
    float loanAmount;
    float interestRate;
    int months;
    float totalAmount;
};

// Declare arrays to store account and loan information
struct Account accounts[MAX_ACCOUNTS];
struct Loan loans[MAX_LOANS];
int currentAccountCount = 0;
int currentLoanCount = 0;

// Admin and Customer login data
char adminUsername[] = "admin";
char adminPassword[] = "admin123";
char customerUsername[] = "customer";
char customerPassword[] = "customer123";

// Global variable to track logged-in status
int isLoggedIn = 0;
int isAdmin = 0; // 1 for Admin, 0 for Customer

// Function to validate login for admin or customer
int login(char username[], char password[]) {
    char inputUsername[50], inputPassword[50];
    
    printf("Enter username: ");
    scanf("%s", inputUsername);
    printf("Enter password: ");
    scanf("%s", inputPassword);
    
    if ((strcmp(inputUsername, adminUsername) == 0 && strcmp(inputPassword, adminPassword) == 0)) {
        isAdmin = 1;
        return 1; // Admin login successful
    } else if ((strcmp(inputUsername, customerUsername) == 0 && strcmp(inputPassword, customerPassword) == 0)) {
        isAdmin = 0;
        return 1; // Customer login successful
    }
    
    return 0; // Invalid credentials
}

// Function to logout the current user
void logout() {
    isLoggedIn = 0;
    isAdmin = 0;
    printf("Logged out successfully.\n");
}

// Function to create a new account
void createAccount() {
    if (currentAccountCount < MAX_ACCOUNTS) {
        struct Account newAccount;
        printf("Enter account number: ");
        scanf("%d", &newAccount.accountNumber);
        printf("Enter name: ");
        getchar(); // To consume newline character
        fgets(newAccount.name, 100, stdin);
        newAccount.name[strcspn(newAccount.name, "\n")] = 0; // Remove newline at the end
        newAccount.balance = 0.0;
        accounts[currentAccountCount++] = newAccount;
        printf("Account created successfully!\n");
    } else {
        printf("Account limit reached!\n");
    }
}

// Function to deposit money into an account
void depositMoney() {
    int accountNumber;
    float depositAmount;
    printf("Enter account number: ");
    scanf("%d", &accountNumber);
    
    for (int i = 0; i < currentAccountCount; i++) {
        if (accounts[i].accountNumber == accountNumber) {
            printf("Enter deposit amount: ");
            scanf("%f", &depositAmount);
            if (depositAmount > 0) {
                accounts[i].balance += depositAmount;
                printf("Amount deposited successfully! New balance: %.2f\n", accounts[i].balance);
                return;
            } else {
                printf("Deposit amount must be positive.\n");
                return;
            }
        }
    }
    printf("Account not found.\n");
}

// Function to withdraw money from an account
void withdrawMoney() {
    int accountNumber;
    float withdrawAmount;
    printf("Enter account number: ");
    scanf("%d", &accountNumber);
    
    for (int i = 0; i < currentAccountCount; i++) {
        if (accounts[i].accountNumber == accountNumber) {
            printf("Enter withdraw amount: ");
            scanf("%f", &withdrawAmount);
            if (withdrawAmount > 0 && withdrawAmount <= accounts[i].balance) {
                accounts[i].balance -= withdrawAmount;
                printf("Amount withdrawn successfully! New balance: %.2f\n", accounts[i].balance);
                return;
            } else {
                printf("Insufficient balance or invalid amount.\n");
                return;
            }
        }
    }
    printf("Account not found.\n");
}

// Function to view account details
void viewAccountDetails() {
    int accountNumber;
    printf("Enter account number: ");
    scanf("%d", &accountNumber);
    
    for (int i = 0; i < currentAccountCount; i++) {
        if (accounts[i].accountNumber == accountNumber) {
            printf("Account Number: %d\n", accounts[i].accountNumber);
            printf("Account Holder: %s\n", accounts[i].name);
            printf("Current Balance: %.2f\n", accounts[i].balance);
            return;
        }
    }
    printf("Account not found.\n");
}

// Function to apply for a loan
void applyForLoan() {
    if (currentLoanCount < MAX_LOANS) {
        struct Loan newLoan;
        printf("Enter account number for loan application: ");
        scanf("%d", &newLoan.accountNumber);
        printf("Enter loan amount: ");
        scanf("%f", &newLoan.loanAmount);
        printf("Enter interest rate (in %%): ");
        scanf("%f", &newLoan.interestRate);
        printf("Enter loan term (in months): ");
        scanf("%d", &newLoan.months);
        
        newLoan.totalAmount = newLoan.loanAmount + (newLoan.loanAmount * newLoan.interestRate / 100);
        loans[currentLoanCount++] = newLoan;
        
        printf("Loan application approved! Total repayable amount: %.2f\n", newLoan.totalAmount);
    } else {
        printf("Loan limit reached!\n");
    }
}

// Function to view all loans
void viewLoans() {
    printf("List of all loans:\n");
    for (int i = 0; i < currentLoanCount; i++) {
        printf("Account Number: %d, Loan Amount: %.2f, Interest Rate: %.2f%%, Term: %d months, Total Repayable: %.2f\n",
               loans[i].accountNumber, loans[i].loanAmount, loans[i].interestRate, loans[i].months, loans[i].totalAmount);
    }
}

// Function to display the menu for admin
void adminMenu() {
    printf("\nAdmin Menu:\n");
    printf("1. Create Account\n");
    printf("2. View All Loans\n");
    printf("3. Logout\n");
}

// Function to display the menu for customers
void customerMenu() {
    printf("\nCustomer Menu:\n");
    printf("1. Deposit Money\n");
    printf("2. Withdraw Money\n");
    printf("3. View Account Details\n");
    printf("4. Apply for Loan\n");
    printf("5. Logout\n");
}

// Main function
int main() {
    int choice;
    int loginStatus = 0;
    
    while (1) {
        if (!loginStatus) {
            printf("\nLogin to your account:\n");
            loginStatus = login(adminUsername, adminPassword); // Login for admin or customer
            if (loginStatus == 0) {
                printf("Invalid credentials, please try again.\n");
                continue;
            } else {
                printf("Login successful!\n");
                isLoggedIn = 1; // User is logged in
            }
        }

        if (isAdmin) {
            while (isLoggedIn) {
                adminMenu();
                printf("Enter your choice: ");
                scanf("%d", &choice);
                
                switch (choice) {
                    case 1:
                        createAccount();
                        break;
                    case 2:
                        viewLoans();
                        break;
                    case 3:
                        logout();
                        loginStatus = 0;
                        break;
                    default:
                        printf("Invalid choice. Please try again.\n");
                }
            }
        } else {
            while (isLoggedIn) {
                customerMenu();
                printf("Enter your choice: ");
                scanf("%d", &choice);
                
                switch (choice) {
                    case 1:
                        depositMoney();
                        break;
                    case 2:
                        withdrawMoney();
                        break;
                    case 3:
                        viewAccountDetails();
                        break;
                    case 4:
                        applyForLoan();
                        break;
                    case 5:
                        logout();
                        loginStatus = 0;
                        break;
                    default:
                        printf("Invalid choice. Please try again.\n");
                }
            }
        }
    }

    return 0;
}
