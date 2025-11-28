# 🅿️ Parking Slot Manager: A Concurrent Java Demo

---

## 1. Project Title
*Parking Slot Manager (PSM): A Real-World Concurrency Challenge in Java*

---

## 2. Quick Overview

Ever tried to park at a busy mall? This project is the Java code behind that headache!

We built the *Parking Slot Manager* to tackle the classic computer science problem of *resource contention* and *thread safety*. The goal was simple: design a software system that can safely hand out parking spots (a limited, shared resource) to multiple arriving vehicles (concurrent threads) without things getting messed up. 

This isn't just a basic demo, though. We had to enforce real-world rules: Vans only fit in big spots, and handicapped drivers must get a special spot if one is open. The entire simulation runs by throwing a "traffic jam" of threads at the system and watching how it uses *ReentrantLock* to keep the peace.

---

## 3. What's Inside (Key Features)

This isn't just a list of classes—it's how we solved the problem:

* *Bulletproof Thread Safety:* We chose java.util.concurrent.locks.ReentrantLock for surgical precision. Why? Because we didn't want the whole lot locked down while one car was trying to park. *Each ParkingSpace manages its *own lock**, so two cars can check different spots at the exact same time. It's fast and avoids unnecessary bottlenecks.
* *Clear Roles:* The design is split up so everything knows its job:
    * Auto: Your basic customer.
    * ParkingSpace: The resource; it says "I'm open" or "No, you can't park here."
    * ParkingLot: The brain; it decides which spot to offer based on all the rules.
    * VehicleFlow: The actual worker thread that runs the simulation.
* *The Rules of the Road:* We built a tight rule-set into the ParkingLot:
    1.  *Vehicle Size:* A SEDAN won't even look at a TINY bike spot. A TRUCK only gets a TRUCK spot.
    2.  *Handicap Only:* If a car needs HC access, it skips every non-HC spot, even if they are big enough.
* *Dynamic Chaos:* Parking times are randomized (1-5 seconds), meaning slots open and close unpredictably. This stress-tests the locking mechanisms better than any fixed delay could.

---

## 4. Tools and Setup Notes

* *Language Used:* Just standard *Java (JDK 8+ is plenty)*. No fancy frameworks needed here.
* *Critical Library:* The only key component is the *ReentrantLock* class. If you're doing concurrency in Java, this is your go-to tool.

### Running It Yourself

*A. Get Ready*

You just need a standard Java environment installed.

*B. The Steps*

1.  *Grab the files:* If you're using Git:
    bash
    git clone [YOUR_REPOSITORY_URL]
    cd [YOUR_REPOSITORY_DIRECTORY]
    
2.  *Compile all the .java files:*
    bash
    javac *.java
    
3.  *Fire it up!* Run the main entry point:
    bash
    java ParkingSimulator
    

You'll get a rapid-fire log output showing the whole parking dance.

---

## 5. How to Test It (Testing and Validation)

The best way to test this project is simply by *watching the log output*—it's designed to expose any concurrency errors immediately.

### A. The Core Test: Did We Break the Laws of Physics?

* *Check for Conflicts:* Scan the log. *You should never, ever see two threads claim the exact same Slot ID (e.g., "slot 104") before one of them leaves it.* If you do, the locks failed!
* *The Race Condition Success:* It's good if you see a car try for a spot but have to keep searching because another thread beat it to the lock. That proves the attemptPark() logic worked correctly to resolve the conflict.

### B. Functional Rules Check

Make sure the strict business logic is holding up:

1.  *Handicap Rule:* Look for any line with (HC). Make sure that car *only* parks in Slot 102 or Slot 201 (the designated HC spots).
2.  *Size Rule:* Check the TRUCK entries. Did they get a smaller spot like 104 or 205? They shouldn't have. They are restricted to the massive truck slots.
3.  *Capacity Check:* The lot only has 10 spots. Once 10 cars are parked, any new threads that arrive should immediately log: "*couldn't park, is driving away.*"
