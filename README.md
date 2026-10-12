#include <stdio.h>

#define MAX 50

typedef struct {
    int id;
    float value;
    float weight;
    float ratio;
    float taken;
} Package;

Package pkg[MAX];

int n = 0;
float capacity = 0;
int entered = 0, ratioDone = 0, sorted = 0, solved = 0;
float totalValue = 0, totalWeight = 0;

void enterDetails(void) {
    int i;

    printf("Enter number of packages (max %d): ", MAX);

    if (scanf("%d", &n) != 1 || n <= 0 || n > MAX) {
        printf("Invalid number of packages.\n");
        n = 0;
        entered = 0;
        return;
    }

    for (i = 0; i < n; i++) {
        pkg[i].id = i + 1;

        printf("Package %d - Value: ", i + 1);
        if (scanf("%f", &pkg[i].value) != 1 ||
            pkg[i].value < 0) {
            printf("Invalid value.\n");
            entered = 0;
            return;
        }

        printf("Package %d - Weight: ", i + 1);
        if (scanf("%f", &pkg[i].weight) != 1 ||
            pkg[i].weight <= 0) {
            printf("Invalid weight. Weight must be positive.\n");
            entered = 0;
            return;
        }

        pkg[i].ratio = 0;
        pkg[i].taken = 0;
    }

    printf("Enter maximum carrying capacity: ");

    if (scanf("%f", &capacity) != 1 || capacity < 0) {
        printf("Invalid capacity.\n");
        entered = 0;
        return;
    }

    entered = 1;
    ratioDone = 0;
    sorted = 0;
    solved = 0;
    totalValue = 0;
    totalWeight = 0;

    printf("Package details saved successfully.\n");
}

void displayDetails(void) {
    int i;

    if (!entered) {
        printf("Please enter package details first.\n");
        return;
    }

    printf("\n%-10s %-10s %-10s", "Package", "Value", "Weight");

    if (ratioDone)
        printf(" %-12s", "Value/Weight");

    printf("\n----------------------------------------------------\n");

    for (i = 0; i < n; i++) {
        printf("%-10d %-10.2f %-10.2f",
               pkg[i].id, pkg[i].value, pkg[i].weight);

        if (ratioDone)
            printf(" %-12.2f", pkg[i].ratio);

        printf("\n");
    }

    printf("Vehicle capacity = %.2f\n", capacity);
}

void calculateRatio(void) {
    int i;

    if (!entered) {
        printf("Please enter package details first.\n");
        return;
    }

    for (i = 0; i < n; i++)
        pkg[i].ratio = pkg[i].value / pkg[i].weight;

    ratioDone = 1;
    sorted = 0;
    solved = 0;

    printf("Value/Weight ratio calculated successfully.\n");
    displayDetails();
}

void sortByRatio(void) {
    int i, j;
    Package temp;

    if (!ratioDone) {
        printf("Please calculate the ratio first.\n");
        return;
    }

    for (i = 0; i < n - 1; i++) {
        for (j = 0; j < n - i - 1; j++) {
            if (pkg[j].ratio < pkg[j + 1].ratio) {
                temp = pkg[j];
                pkg[j] = pkg[j + 1];
                pkg[j + 1] = temp;
            }
        }
    }

    sorted = 1;
    solved = 0;

    printf("Packages sorted by decreasing ratio.\n");
    displayDetails();
}

void findMaxValue(void) {
    int i;
    float remaining;

    if (!sorted) {
        printf("Please sort the packages first.\n");
        return;
    }

    remaining = capacity;
    totalValue = 0;
    totalWeight = 0;

    for (i = 0; i < n; i++)
        pkg[i].taken = 0;

    for (i = 0; i < n && remaining > 0; i++) {
        if (pkg[i].weight <= remaining) {
            pkg[i].taken = 1.0f;
            remaining -= pkg[i].weight;
            totalWeight += pkg[i].weight;
            totalValue += pkg[i].value;
        } else {
            pkg[i].taken = remaining / pkg[i].weight;
            totalValue += pkg[i].value * pkg[i].taken;
            totalWeight += remaining;
            remaining = 0;
        }
    }

    solved = 1;
    printf("Maximum value obtained = %.2f\n", totalValue);
}

void displaySelected(void) {
    int i;

    if (!solved) {
        printf("Please find the maximum value first.\n");
        return;
    }

    printf("\n%-10s %-10s %-10s %-10s %-12s\n",
           "Package", "Value", "Weight", "Fraction", "Value Taken");

    printf("-----------------------------------------------------------\n");

    for (i = 0; i < n; i++) {
        if (pkg[i].taken > 0) {
            printf("%-10d %-10.2f %-10.2f %-10.2f %-12.2f\n",
                   pkg[i].id, pkg[i].value, pkg[i].weight,
                   pkg[i].taken, pkg[i].value * pkg[i].taken);
        }
    }

    printf("-----------------------------------------------------------\n");
    printf("Total weight used: %.2f / %.2f\n", totalWeight, capacity);
    printf("Maximum value obtained: %.2f\n", totalValue);
    printf("Time complexity: O(n^2) using bubble sort.\n");
    printf("Selection loop complexity: O(n).\n");
}

int main(void) {
    int choice;

    do {
        printf("\n===== SMART DELIVERY PLANNING - FRACTIONAL KNAPSACK =====\n");
        printf("1. Enter Package Details\n");
        printf("2. Display Package Details\n");
        printf("3. Calculate Value/Weight Ratio\n");
        printf("4. Sort Packages by Ratio\n");
        printf("5. Find Maximum Value\n");
        printf("6. Display Selected Packages\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1) {
            printf("Invalid input. Exiting program.\n");
            break;
        }

        switch (choice) {
            case 1: enterDetails(); break;
            case 2: displayDetails(); break;
            case 3: calculateRatio(); break;
            case 4: sortByRatio(); break;
            case 5: findMaxValue(); break;
            case 6: displaySelected(); break;
            case 7: printf("Exiting program.\n"); break;
            default: printf("Invalid choice. Try again.\n");
        }

    } while (choice != 7);

    return 0;
}

