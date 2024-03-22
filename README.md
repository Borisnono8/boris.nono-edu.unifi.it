include <iostream>
#include <fstream>
#include <vector>
#include <ctime>
#include <cassert>

class Activity {
    std::string name;
    bool completed;
    time_t deadline;

public:
    Activity(std::string name, time_t deadline) : name(name), completed(false), deadline(deadline) {}

    std::string getName() const {
        return name;
    }

    bool isCompleted() const {
        return completed;
    }

    void setCompleted(bool status) {
        completed = status;
    }

    time_t getDeadline() const {
        return deadline;
    }
};

class ActivityList {
    std::string name;
    std::vector<Activity> activities;

public:
    void addActivity(const Activity& activity) {
        activities.push_back(activity);
    }

    void modifyActivity(const std::string& oldName, const std::string& newName) {
        for (Activity& activity : activities) {
            if (activity.getName() == oldName) {
                activity = Activity(newName, activity.getDeadline());
            }
        }
    }

    void removeActivity(const std::string& name) {
        activities.erase(
            std::remove_if(activities.begin(), activities.end(),
                [name](const Activity& activity) {
                    return activity.getName() == name;
                }),
            activities.end());
    }

    int getCompletedActivitiesCount() const {
        int count = 0;
        for (const Activity& activity : activities) {
            if (activity.isCompleted()) {
                count++;
            }
        }
        return count;
    }

    int getTotalActivitiesCount() const {
        return activities.size();
    }

    void saveToFile(const std::string& filename) const {
        std::ofstream file(filename);
        if (file.is_open()) {
            for (const Activity& activity : activities) {
                file << activity.getName() << "," << activity.getDeadline() << "," << activity.isCompleted() << std::endl;
            }
            file.close();
        }
    }

    void loadFromFile(const std::string& filename) {
        std::ifstream file(filename);
        if (file.is_open()) {
            activities.clear();
            std::string line;
            while (std::getline(file, line)) {
                std::string name;
                time_t deadline;
                bool completed;
                // parse line and create Activity object
                //...
                activities.emplace_back(name, deadline);
            }
            file.close();
        }
    }
};

// Unit Testing
void testActivityList() {
    ActivityList list;
    list.addActivity(Activity("Task 1", time(0)));
    assert(list.getTotalActivitiesCount() == 1);
    assert(list.getCompletedActivitiesCount() == 0);
    
    list.addActivity(Activity("Task 2", time(0)));
    list.addActivity(Activity("Task 3", time(0)));
    list.addActivity(Activity("Task 4", time(0)));
    assert(list.getTotalActivitiesCount() == 4);
    
    list.modifyActivity("Task 2", "Task 5");
    assert(list.getTotalActivitiesCount() == 4);
    list.removeActivity("Task 1");
    assert(list.getTotalActivitiesCount() == 3);
}

int main() {
    testActivityList();
    std::cout << "All tests passed!" << std::endl;
    return 0;
}
