عنوان المصنف: توصيل للسيارة
وصف المصنف: يقوم البرنامج بتنظيم العلاقة بين نقطة البيع والمشتري عن بعد وخادم صف السيارات

/* Abdulmohsen Rajeh 
************************************************************************************************************************
*The code below is a Proof of Concept that made the assistant of Large Language Model to simulate the concept of       * 
*regulating the relationship between the point of sale, the online buyer, and the parking attendant (Valet) to         * 
*deliver the product to the car once the customer arrived to the agreed location (Parking Code)                        *
*The Core data set are: (OrderID, VehicleID, ParkingCode, ValetID)                                                     *
*Copyright Reserved 2025 to Abdulmohsen Rajeh, Saudi Arabia                                                            *  
************************************************************************************************************************/
#include <iostream>
#include <vector>
#include <cmath>
#include <limits> // For std::numeric_limits
#include <string>
struct CarPark {
    std::string carParkId;
    std::string address;
    double latitude;
    double longitude;
    int capacity;
    int currentOccupancy;
};
double haversine(double lat1, double lon1, double lat2, double lon2) {
    const double R = 6371.0; // Earth's radius in kilometers
    double dLat = (lat2 - lat1) * M_PI / 180.0; // Convert degrees to radians
    double dLon = (lon2 - lon1) * M_PI / 180.0;
    double a = sin(dLat / 2) * sin(dLat / 2) +
               cos(lat1 * M_PI / 180.0) * cos(lat2 * M_PI / 180.0) * 
               sin(dLon / 2) * sin(dLon / 2);
    double c = 2 * atan2(sqrt(a), sqrt(1 - a));
    return R * c;
}
CarPark* findNearestCarPark(const std::vector<CarPark>& carParks, double deliveryLat, double deliveryLon) {
    CarPark* nearestCarPark = nullptr;
    double minDistance = std::numeric_limits<double>::max(); // Initialize to max possible distance

    for (const auto& carPark : carParks) {
        double distance = haversine(deliveryLat, deliveryLon, carPark.latitude, carPark.longitude);
       
        if (distance < minDistance && (carPark.currentOccupancy < carPark.capacity)) {
            minDistance = distance;
            nearestCarPark = const_cast<CarPark*>(&carPark); // Update nearest car park
        }
    }

    return nearestCarPark; 
}

int main() {
    std::vector<CarPark> carParks = {
        {"CP1", "Car Park 1", 40.7128, -74.0060, 100, 80}, // Example coordinates for New York
        {"CP2", "Car Park 2", 40.7138, -74.0050, 50, 50}, // Full car park
        {"CP3", "Car Park 3", 40.7148, -74.0040, 30, 10}  // Example place
    };

    double deliveryLat;
    double deliveryLon;

    std::cout << "Enter your delivery address latitude: ";
    std::cin >> deliveryLat;
    std::cout << "Enter your delivery address longitude: ";
    std::cin >> deliveryLon;
    std::string arrivalTime;
    std::cout << "Enter your estimated arrival time (e.g., 15:30): ";
    std::cin >> arrivalTime;
    CarPark* nearestCarPark = findNearestCarPark(carParks, deliveryLat, deliveryLon);
    if (nearestCarPark != nullptr) {
        std::cout << "The nearest car park is: " << nearestCarPark->address << "\n";
        std::cout << "Your estimated arrival time: " << arrivalTime << "\n";
        std::string deliveryPersonInput;
        std::cout << "Enter the car park number for delivery: ";
        std::cin >> deliveryPersonInput;
        if (deliveryPersonInput == nearestCarPark->carParkId) {
            std::cout << "Delivery personnel has successfully sent to car park: " << nearestCarPark->address << "\n";
        } else {
            std::cout << "Invalid car park number. Please ensure you have entered the correct number.\n";
        }
    } else {
        std::cout << "No available car parks.\n";
    }

    return 0;
}
