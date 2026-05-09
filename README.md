# #include <iostream>
#include <fstream>
#include <iomanip>
#include <cstring>
#include <limits>

using namespace std;

class Student
{
private:
    int rollNo;
    char name[50];
    float marks;

public:
    void input()
    {
        cout << "\nEnter Roll Number: ";
        cin >> rollNo;

        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "Enter Name: ";
        cin.getline(name, 50);

        cout << "Enter Marks: ";
        cin >> marks;
    }

    void display()
    {
        cout << left << setw(10) << rollNo
             << setw(25) << name
             << setw(10) << marks << endl;
    }

    int getRollNo()
    {
        return rollNo;
    }

    void update()
    {
        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "Enter New Name: ";
        cin.getline(name, 50);

        cout << "Enter New Marks: ";
        cin >> marks;
    }
};

void addStudent()
{
    Student s;

    ofstream file("students.dat", ios::binary | ios::app);

    s.input();

    file.write((char*)&s, sizeof(s));

    file.close();

    cout << "\nStudent Record Added Successfully!\n";
}

void displayStudents()
{
    Student s;

    ifstream file("students.dat", ios::binary);

    if(!file)
    {
        cout << "\nNo Records Found!\n";
        return;
    }

    cout << "\n--------------------------------------------------\n";
    cout << left << setw(10) << "RollNo"
         << setw(25) << "Name"
         << setw(10) << "Marks" << endl;

    cout << "--------------------------------------------------\n";

    while(file.read((char*)&s, sizeof(s)))
    {
        s.display();
    }

    file.close();
}

void searchStudent()
{
    Student s;
    int roll;
    bool found = false;

    ifstream file("students.dat", ios::binary);

    cout << "\nEnter Roll Number to Search: ";
    cin >> roll;

    while(file.read((char*)&s, sizeof(s)))
    {
        if(s.getRollNo() == roll)
        {
            cout << "\nStudent Found:\n\n";

            cout << left << setw(10) << "RollNo"
                 << setw(25) << "Name"
                 << setw(10) << "Marks" << endl;

            s.display();

            found = true;
            break;
        }
    }

    if(!found)
    {
        cout << "\nStudent Not Found!\n";
    }

    file.close();
}

void updateStudent()
{
    Student s;
    int roll;
    bool found = false;

    fstream file("students.dat", ios::binary | ios::in | ios::out);

    cout << "\nEnter Roll Number to Update: ";
    cin >> roll;

    while(file.read((char*)&s, sizeof(s)))
    {
        if(s.getRollNo() == roll)
        {
            cout << "\nCurrent Record:\n\n";

            cout << left << setw(10) << "RollNo"
                 << setw(25) << "Name"
                 << setw(10) << "Marks" << endl;

            s.display();

            cout << "\nEnter New Details:\n";

            s.update();

            int pos = file.tellg();

            file.seekp(pos - sizeof(s));

            file.write((char*)&s, sizeof(s));

            found = true;

            cout << "\nRecord Updated Successfully!\n";

            break;
        }
    }

    if(!found)
    {
        cout << "\nStudent Not Found!\n";
    }

    file.close();
}

void deleteStudent()
{
    Student s;
    int roll;
    bool found = false;

    ifstream file("students.dat", ios::binary);

    ofstream temp("temp.dat", ios::binary);

    cout << "\nEnter Roll Number to Delete: ";
    cin >> roll;

    while(file.read((char*)&s, sizeof(s)))
    {
        if(s.getRollNo() == roll)
        {
            found = true;
        }
        else
        {
            temp.write((char*)&s, sizeof(s));
        }
    }

    file.close();
    temp.close();

    remove("students.dat");
    rename("temp.dat", "students.dat");

    if(found)
    {
        cout << "\nRecord Deleted Successfully!\n";
    }
    else
    {
        cout << "\nStudent Not Found!\n";
    }
}

int main()
{
    int choice;

    do
    {
        system("cls");

        cout << "\n========== STUDENT MANAGEMENT SYSTEM ==========\n";

        cout << "\n1. Add Student";
        cout << "\n2. Display Students";
        cout << "\n3. Search Student";
        cout << "\n4. Update Student";
        cout << "\n5. Delete Student";
        cout << "\n6. Exit";

        cout << "\n\nEnter Your Choice: ";
        cin >> choice;

        switch(choice)
        {
            case 1:
                addStudent();
                break;

            case 2:
                displayStudents();
                break;

            case 3:
                searchStudent();
                break;

            case 4:
                updateStudent();
                break;

            case 5:
                deleteStudent();
                break;

            case 6:
                cout << "\nExiting Program...\n";
                break;

            default:
                cout << "\nInvalid Choice!\n";
        }

        if(choice != 6)
        {
            cout << "\n\nPress Enter to Continue...";
            cin.ignore();
            cin.get();
        }

    } while(choice != 6);

    return 0;
}
