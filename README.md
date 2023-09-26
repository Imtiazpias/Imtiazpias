#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// User structure to store user information
typedef struct User {
    char username[50];
    char password[50];
    int role; // 1 for Doctor, 2 for Patient
    struct User* next;
} User;

// Patient structure to store patient information
typedef struct Patient {
    int id;
    char name[50];
    int age;
    char gender;
    char mobileNumber[15];
    char address[100];
    char diseaseExplanation[200];
    char uniqueID[6];
    char password[5]; // 4-digit password
    struct Patient* next;
} Patient;

// Global user and patient linked lists
User* userList = NULL;
Patient* patientList = NULL;

// Function to generate a unique ID
int generateUniqueId() {
    // Use a simple random number generator for demonstration
    return rand() % 10000 + 1;
}

// Function to generate a random 4-digit password
void generatePassword(char password[]) {
    static const char charset[] = "0123456789";
    for (int i = 0; i < 4; i++) {
        int index = rand() % (sizeof(charset) - 1);
        password[i] = charset[index];
    }
    password[4] = '\0';
}

// Function to create a new user node
User* createUser(char username[], char password[], int role) {
    User* newUser = (User*)malloc(sizeof(User));
    strcpy(newUser->username, username);
    strcpy(newUser->password, password);
    newUser->role = role;
    newUser->next = NULL;
    return newUser;
}

// Function to add a user to the user linked list
void addUser(User** userList, char username[], char password[], int role) {
    User* newUser = createUser(username, password, role);
    newUser->next = *userList;
    *userList = newUser;
}

// Function to authenticate users
User* authenticate(User* userList, char username[], char password[]) {
    User* current = userList;
    while (current != NULL) {
        if (strcmp(current->username, username) == 0 && strcmp(current->password, password) == 0) {
            return current;
        }
        current = current->next;
    }
    return NULL;
}

// Function to create a new patient node
Patient* createPatient(char name[], int age, char gender, char mobileNumber[], char address[], char diseaseExplanation[]) {
    Patient* newPatient = (Patient*)malloc(sizeof(Patient));
    newPatient->id = generateUniqueId();
    strcpy(newPatient->name, name);
    newPatient->age = age;
    newPatient->gender = gender;
    strcpy(newPatient->mobileNumber, mobileNumber);
    strcpy(newPatient->address, address);
    strcpy(newPatient->diseaseExplanation, diseaseExplanation);
    generatePassword(newPatient->password); // Generate a 4-digit password
    sprintf(newPatient->uniqueID, "%d", newPatient->id); // Convert ID to string and store in uniqueID
    newPatient->next = NULL;
    return newPatient;
}

// Function to add a patient to the patient list
void addPatient(char name[], int age, char gender, char mobileNumber[], char address[], char diseaseExplanation[]) {
    Patient* newPatient = createPatient(name, age, gender, mobileNumber, address, diseaseExplanation);
    newPatient->next = patientList;
    patientList = newPatient;
}

// Function to display patient details
void displayPatientDetails(Patient* patient) {
    printf("\nPatient ID: %d\n", patient->id);
    printf("Patient Name: %s\n", patient->name);
    printf("Age: %d\n", patient->age);
    printf("Gender: %c\n", patient->gender);
    printf("Mobile Number: %s\n", patient->mobileNumber);
    printf("Address: %s\n", patient->address);
    printf("Disease Explanation: %s\n", patient->diseaseExplanation);
    printf("Unique ID: %s\n", patient->uniqueID); // Display unique ID
    printf("Password: %s\n", patient->password);
}

// Function to display all new patient registration details
void displayNewPatientRegistrations() {
    Patient* current = patientList;
    printf("\nNew Patient Registrations:\n");
    while (current != NULL) {
        displayPatientDetails(current);
        current = current->next;
    }
}

// Function to display new registered patient details
void displayNewRegisteredPatients() {
    Patient* current = patientList;
    int found = 0; // Flag to check if new registered patients are found
    printf("\nNew Registered Patients:\n");
    while (current != NULL) {
        // Check if the patient's password is not "0000" to identify new patients
        if (strcmp(current->password, "0000") != 0) {
            displayPatientDetails(current);
            found = 1; // Set the flag to indicate new registered patients were found
        }
        current = current->next;
    }

    if (!found) {
        printf("No new registered patients found.\n");
    }
}

int main() {
    srand(time(NULL)); // Seed for random number generation

    // Initialize users (You can add more users here)
    addUser(&userList, "doctor1", "password1", 1);
    addUser(&userList, "patient1", "password1", 2);

    int isDoctorLoggedIn = 0; // Flag to check if the doctor is logged in

    while (1) {
        printf("\nHealth Care Analysis\n");
        if (isDoctorLoggedIn) {
            printf("1. View New Registered Patients\n");
        } else {
            printf("1. Login as Doctor\n");
        }
        printf("2. Login as Patient\n");
        printf("3. New Patient Registration\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        int choice;
        scanf("%d", &choice);

        switch (choice) {
            case 1: {
                if (isDoctorLoggedIn) {
                    // Display new registered patients
                    printf("\nDoctor, here are the New Registered Patients:\n");
                    displayNewRegisteredPatients();
                } else {
                    char username[50];
                    char password[50];
                    printf("Enter username: ");
                    scanf("%s", username);
                    printf("Enter password: ");
                    scanf("%s", password);

                    User* user = authenticate(userList, username, password);
                    if (user != NULL && user->role == 1) {
                        printf("Welcome, Doctor %s!\n", user->username);
                        isDoctorLoggedIn = 1; // Set the doctor login flag
                    } else {
                        printf("Invalid credentials for doctor login. Please try again.\n");
                    }
                }
                break;
            }
            case 2: {
                // Code for patient login can be added here
                break;
            }
            case 3: {
                char name[50];
                int age;
                char gender;
                char mobileNumber[15];
                char address[100];
                char diseaseExplanation[200];

                printf("New Patient Registration\n");
                printf("Patient Name: ");
                scanf("%s", name);
                printf("Age: ");
                scanf("%d", &age);
                printf("Gender (M/F): ");
                scanf(" %c", &gender);
                printf("Mobile Number: ");
                scanf("%s", mobileNumber);
                printf("Address: ");
                scanf(" %[^\n]", address);
                printf("Disease Explanation: ");
                scanf(" %[^\n]", diseaseExplanation);

                addPatient(name, age, gender, mobileNumber, address, diseaseExplanation);

                Patient* newPatient = patientList;
                printf("\nRegistration Successful! Here are your details:\n");
                displayPatientDetails(newPatient);

                printf("\nMessage: Doctor will contact you within 24 hours.\n");
                break;
            }
            case 4:
                printf("Exiting...\n");
                exit(0);
            default:
                printf("Invalid choice. Please try again.\n");
                break;
        }
    }

    return 0;
}
