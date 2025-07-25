# To-do-list

#include <iostream>
#include <vector>
#include <fstream>
#include <string>
#include <algorithm>

using namespace std;

struct Task {
    string description;
    string category;
    bool completed;
};

class ToDoList {
private:
    vector<Task> tasks;

public:
    void addTask(const string& description, const string& category) {
        tasks.push_back({description, category, false});
    }

    void viewTasks() {
        for (const auto& task : tasks) {
            cout << (task.completed ? "[X] " : "[ ] ") << task.description << " [" << task.category << "]" << endl;
        }
    }

    void markCompleted(int index) {
        if (index >= 0 && index < tasks.size()) {
            tasks[index].completed = true;
        }
    }

    void removeTask(int index) {
        if (index >= 0 && index < tasks.size()) {
            tasks.erase(tasks.begin() + index);
        }
    }

    void saveToFile(const string& filename) {
        ofstream file(filename);
        for (const auto& task : tasks) {
            file << task.description << "|" << task.category << "|" << task.completed << endl;
        }
        file.close();
    }

    void loadFromFile(const string& filename) {
        ifstream file(filename);
        string line;
        while (getline(file, line)) {
            size_t pos1 = line.find('|');
            size_t pos2 = line.find('|', pos1 + 1);
            string description = line.substr(0, pos1);
            string category = line.substr(pos1 + 1, pos2 - pos1 - 1);
            bool completed = (line.substr(pos2 + 1) == "1");
            tasks.push_back({description, category, completed});
        }
        file.close();
    }

    void filterByCategory(const string& category) {
        for (const auto& task : tasks) {
            if (task.category == category) {
                cout << (task.completed ? "[X] " : "[ ] ") << task.description << endl;
            }
        }
    }
};

int main() {
    ToDoList todoList;
    todoList.loadFromFile("tasks.txt");

    int choice;
    do {
        cout << "1. Add Task\n2. View Tasks\n3. Mark Task as Completed\n4. Remove Task\n5. Filter by Category\n6. Save and Exit\n";
        cin >> choice;
        cin.ignore();

        if (choice == 1) {
            string description, category;
            cout << "Enter task description: ";
            getline(cin, description);
            cout << "Enter task category: ";
            getline(cin, category);
            todoList.addTask(description, category);
        } else if (choice == 2) {
            todoList.viewTasks();
        } else if (choice == 3) {
            int index;
            cout << "Enter task index to mark as completed: ";
            cin >> index;
            todoList.markCompleted(index);
        } else if (choice == 4) {
            int index;
            cout << "Enter task index to remove: ";
            cin >> index;
            todoList.removeTask(index);
        } else if (choice == 5) {
            string category;
            cout << "Enter category to filter: ";
            cin >> category;
            todoList.filterByCategory(category);
        }
    } while (choice != 6);

    todoList.saveToFile("tasks.txt");
    return 0;
}
