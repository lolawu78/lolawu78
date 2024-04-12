/*
Miao Dan Wu Feng
COP 3223C
Lab 13 - Employee Management
April 11, 2024
*/

#include <stdio.h>
#include <string.h>
#define SIZE_EMP 100
#define SIZE_DEPT 50

// create structs for employee and department
typedef struct Employee {
  char eid[10];
  char lname[50];
  int sal;
} Employee;

typedef struct Dept {
  char eid[10];
  char dname[50];
} Dept;

// read the employee file and return the total num of employees
int readEmp(Employee ArrayOfEmployees[], char fileName[]) {
  // read the file
  FILE *f_emp = fopen(fileName, "r");
  // declare variables for scan the id and name
  char eId[10];
  char name[100];
  int salary;
  int empCount = 0;
  // scan file
  while (fscanf(f_emp, "%s %s %d", eId, name, &salary) != EOF) {
    // using strcpy to copy those into the struct
    strcpy(ArrayOfEmployees[empCount].eid, eId);
    strcpy(ArrayOfEmployees[empCount].lname, name);
    ArrayOfEmployees[empCount].sal = salary;
    empCount++;
  }
  fclose(f_emp);
  return empCount;
}

// read the department file and return the total num of departments
void readDept(Dept ArrayOfDepartments[], int *numOfDept, char depetFileName[]) {
  // read the file
  FILE *f_dept = fopen(depetFileName, "r");
  // declare variable
  char eId[10];
  char name[100];
  int deptCount = 0;
  // scan the file
  while (fscanf(f_dept, "%s %s", eId, name) != EOF) {
    strcpy(ArrayOfDepartments[deptCount].eid, eId);
    strcpy(ArrayOfDepartments[deptCount].dname, name);
    deptCount++;
  }
  fclose(f_dept);
  *numOfDept = deptCount;
}

// print the table
void printAll(Employee Arr[], int totalEmp, Dept ArrayOfDepartments[],
              int totalDept) {
  printf("Printing the list of Employees:\n=================\n");
  // for loop to go through the employee
  for (int i = 0; i < totalEmp; i++) {
    printf("ID: %s\tLName: %s\tSalary: %d\t", Arr[i].eid, Arr[i].lname,
           Arr[i].sal);

    // check which department that the employee works at
    for (int j = 0; j < totalDept; j++) {
      if (strcmp(ArrayOfDepartments[j].eid, Arr[i].eid) == 0) {
        printf("\tDepartment: %s", ArrayOfDepartments[j].dname);
        break;
      }
      if (j == totalDept)
        printf("\tDepartment: None");
    }
    printf("\n");
  }
}

// search for employee and print the information
void search_employee(Employee Arr[], int totalEmp, Dept ArrayOfDepartments[],
                     int totalDept, char qStr[]) {
  int key = 0;
  for (int i = 0; i < totalEmp; i++) {
    if (strcmp(Arr[i].lname, qStr) == 0){
      for (int j = 0; j < totalDept; j++) {
        if(strcmp(Arr[i].eid, ArrayOfDepartments[j].eid) == 0) {
          printf("ID: %s\tLName: %s\tSalary: %d\tDepartment: %s\t", Arr[i].eid, Arr[i].lname, Arr[i].sal, ArrayOfDepartments[j].dname);
          key = 1;
          break;
        }
      }
    }
  }
  if (key == 0)
    printf("Employee %s Not Found\n", qStr);
}

// search for department and print the info
int totalSal_dept(Employee Arr[], int totalEmp, Dept ArrayOfDepartments[],
                  int totalDept, char qStr[]) {
  int total = 0;
  for (int i = 0; i < totalDept; i++) {
    if (strcmp(ArrayOfDepartments[i].dname, qStr) == 0) {
      for (int j = 0; j < totalEmp; j++) {
        if (strcmp(ArrayOfDepartments[i].eid, Arr[j].eid) == 0)
          total += Arr[j].sal;
      }
    }
  }
  return total;
}

int main(void) {

  Employee EmpList[SIZE_EMP];
  Dept DeptList[SIZE_DEPT];

  // declare variables
  char empFileName[50], deptFileName[50], queryFileName[50];
  // scan the file name from the user
  scanf("%s %s %s", empFileName, deptFileName, queryFileName);

  // call readEmp()
  int total_emp = readEmp(EmpList, empFileName);
  int total_dept;
  // call readDept()
  readDept(DeptList, &total_dept, deptFileName);
  // call printAll()
  printAll(EmpList, total_emp, DeptList, total_dept);

  // scan Query.txt
  printf("Query Phase\n");
  // process the query by calling sesearch_employee() and totalSal_dept()
  // function
  FILE *f_query = fopen(queryFileName, "r");
  int queryType;
  char qStr[100];
  int totalSal;
  while (fscanf(f_query, "%d %s", &queryType, qStr) != EOF) {
    if (queryType == 1) {
      search_employee(EmpList, total_emp, DeptList, total_dept, qStr);
    } else if (queryType == 2) {
      totalSal = totalSal_dept(EmpList, total_emp, DeptList, total_dept, qStr);
      printf("Total Salary of Department %s is %d\n", qStr, totalSal);
    }
  }
  fclose(f_query);

  return 0;
}
