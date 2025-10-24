#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_SEATS 5

struct Passenger {
    int id;
    char name[50];
    int age;
    char gender[10];
    int seatNo;
    struct Passenger *next;
};

struct Passenger *booked = NULL;
struct Passenger *waiting = NULL;
int bookedCount = 0, nextID = 1;

// Function prototypes
void bookTicket();
void cancelTicket();
void viewBooked();
void viewWaiting();
void enqueue(struct Passenger *p);
struct Passenger* dequeue();

int main() {
    int choice;
    while (1) {
        printf("\n=== RAILWAY RESERVATION SYSTEM ===\n");
        printf("1. Book Ticket\n2. Cancel Ticket\n3. View Booked List\n4. View Waiting List\n5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

  switch (choice) {
            case 1: bookTicket(); break;
            case 2: cancelTicket(); break;
            case 3: viewBooked(); break;
            case 4: viewWaiting(); break;
            case 5: exit(0);
            default: printf("Invalid choice!\n");
        }
    }
}

void bookTicket() {
    struct Passenger *p = (struct Passenger*)malloc(sizeof(struct Passenger));
    p->id = nextID++;
    printf("Enter Name: "); scanf("%s", p->name);
    printf("Enter Age: "); scanf("%d", &p->age);
    printf("Enter Gender: "); scanf("%s", p->gender);
    p->next = NULL;

 if (bookedCount < MAX_SEATS) {
        p->seatNo = bookedCount + 1;
        p->next = booked;
        booked = p;
        bookedCount++;
        printf("Ticket Booked! Seat No: %d (Passenger ID: %d)\n", p->seatNo, p->id);
    } else {
        enqueue(p);
        printf("All seats full! Added to waiting list.\n");
    }
}

void cancelTicket() {
    int id;
    printf("Enter Passenger ID to cancel: ");
    scanf("%d", &id);

  struct Passenger *temp = booked, *prev = NULL;
    while (temp != NULL && temp->id != id) {
        prev = temp;
        temp = temp->next;
    }

  if (temp == NULL) {
        printf("Passenger not found!\n");
        return;
    }

  if (prev == NULL) booked = temp->next;
    else prev->next = temp->next;

   printf("Ticket cancelled for %s (ID %d)\n", temp->name, temp->id);
    free(temp);
    bookedCount--;

   // Move from waiting list if available
    struct Passenger *wait = dequeue();
    if (wait != NULL) {
        wait->seatNo = bookedCount + 1;
        wait->next = booked;
        booked = wait;
        bookedCount++;
        printf("Moved %s from waiting
        list to seat %d\n", wait->name, wait->seatNo);
    }
}

void viewBooked() {
    printf("\n--- Booked Passengers ---\n");
    struct Passenger *temp = booked;
    while (temp != NULL) {
        printf("ID:%d | Name:%s | Age:%d | Seat:%d\n", temp->id, temp->name, temp->age, temp->seatNo);
        temp = temp->next;
    }
}

void viewWaiting() {
    printf("\n--- Waiting List ---\n");
    struct Passenger *temp = waiting;
    while (temp != NULL) {
        printf("ID:%d | Name:%s | Age:%d\n", temp->id, temp->name, temp->age);
        temp = temp->next;
    }
}

void enqueue(struct Passenger *p) {
    if (waiting == NULL) waiting = p;
    else {
        struct Passenger *temp = waiting;
        while (temp->next != NULL) temp = temp->next;
        temp->next = p;
    }
}

struct Passenger* dequeue() {
    if (waiting == NULL) return NULL;
    struct Passenger *temp = waiting;
    waiting = waiting->next;
    temp->next = NULL;
    return temp;
}
