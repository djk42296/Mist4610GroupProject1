# Mist4610GroupProject1
## Team Name
59925 Group 3

## Team Members
1. David Kenny @djk42296
2. Sahasra Manchikanti @sahasra-22
3. Grant Harris
4. Joseph Enck @havemercy74
5. Sofia Drescher

## Problem Description
The task at hand is to design and develop a relational database for the overall operations of a university housing system. The primary entity in the model is the Residence Hall entity of the housing system, with each Residence Hall representing an individual dormitory facility that the university owns and maintains across campus. The Residence Hall functions alongside dorm floors, student rooms, resident assistants, maintenance requests, and parking assignments that serve the students living within university housing. We aim to accurately represent these relationships, create sample data, and populate the entities and their corresponding attributes with this information. In addition, we intend to perform functional queries on the data so that they may provide meaningful insight into the housing system and its day-to-day operations.

## Data Model
The data model represents the core operations of a university housing management system. It is designed to store information about residence halls, dorm floors, rooms, students, resident assistants (RAs), university housing staff, maintenance requests, inspections, and parking assignments. Each of these entities interacts in ways that reflect how a real university housing system functions on a daily basis.
The Residence Hall entity serves as the foundation of the model, with each hall containing multiple Dorm Floors, and each floor consisting of several Rooms. The Students entity stores personal information about each student living on campus, such as their name, phone number, class year, and assigned RA. Each student is connected to a specific room through the DormAssignments table, allowing the database to track which student resides in which room.
Each Resident Assistant is also linked to both a Dorm Floor and a Student record, representing that RAs are students themselves who oversee a particular floor. The Inspections entity stores information about room inspections conducted by RAs, including the inspection score and the room being inspected.
The model also includes MaintenanceRequests, which connect rooms to UGAStaff members responsible for resolving maintenance issues. This allows the system to record who submitted the request, which room it pertains to, and which staff member completed the work. Finally, the Parking entity tracks parking locations and associates them with students who have been assigned parking spaces.
Overall, the database supports storage of all key data related to housing logistics, such as student assignments, staff information, maintenance activity, and inspection records. However, it does not store financial data such as rent payments or billing, nor does it include academic information like student grades or course enrollment. The model focuses strictly on operational and residential management within university housing.

<img width="812" height="450" alt="image" src="https://github.com/user-attachments/assets/b9f2b211-bbfc-41c2-b790-e991aeaab1f1" />

## Data Dictionary
<img width="810" height="342" alt="image" src="https://github.com/user-attachments/assets/7fc52ce5-1432-4dc1-9beb-76e6276638de" />

<img width="892" height="501" alt="image" src="https://github.com/user-attachments/assets/2bba2079-384d-439a-b0d0-1f7fdad01af8" />

<img width="827" height="547" alt="image" src="https://github.com/user-attachments/assets/62970e4e-4a53-402f-a987-788ba7ab3641" />

<img width="826" height="486" alt="image" src="https://github.com/user-attachments/assets/100f5861-c7aa-420d-bbbf-2c2c5fbf0440" />

<img width="818" height="507" alt="image" src="https://github.com/user-attachments/assets/a2b9b7c7-7ecb-400b-9f97-6d36c5dc8bf6" />

<img width="812" height="277" alt="image" src="https://github.com/user-attachments/assets/35dd08da-f7b2-44b6-b5b9-f62f8b27d65f" />

<img width="838" height="582" alt="image" src="https://github.com/user-attachments/assets/0fdd7272-16cc-484e-8dbf-d5cc2104d870" />

<img width="867" height="730" alt="image" src="https://github.com/user-attachments/assets/4b9e78ee-be77-47e3-8bf7-f37759931a34" />

<img width="830" height="466" alt="image" src="https://github.com/user-attachments/assets/1a6ec1d7-4de8-46cd-b5e3-19734a38a7b6" />

## Queries
<img width="831" height="487" alt="image" src="https://github.com/user-attachments/assets/270bc571-4403-4892-8c43-2af28a2c62f5" />

# Query 1
QUERY 1 - ResidenceHallRequests

SELECT ResidenceHall.address, COUNT(MaintainanceRequests.requestID) AS NumberOfRequests

FROM MaintainanceRequests

JOIN Rooms ON MaintainanceRequests.roomID = Rooms.roomID

JOIN DormFloor ON Rooms.DormFloor_floorID = DormFloor.floorID

JOIN ResidenceHall ON DormFloor.residenceHallID = ResidenceHall.residenceHallID

GROUP BY ResidenceHall.address

HAVING COUNT(MaintainanceRequests.requestID) > 1;

<img width="802" height="142" alt="image" src="https://github.com/user-attachments/assets/3d556fd1-6ec6-4b18-9b13-ad7dc74c22eb" />

Query 1 allows managers to see which residence halls have had the most maintenance requests. This helps managers identify which buildings may be experiencing more issues and therefore require more frequent maintenance checks, additional staff, or updated facilities. By listing only halls with more than one request, the query filters out isolated incidents and focuses on areas with recurring problems that may need further investigation.

# Query 2
Query 2 - InspectionStats

SELECT ResidenceHall.address, COUNT(Inspections.inspectionID) AS TotalInspections,

(SELECT COUNT(*) FROM Inspections 

JOIN Rooms ON Inspections.roomID = Rooms.roomID

JOIN DormFloor ON Rooms.DormFloor_floorID = DormFloor.floorID 

WHERE DormFloor.residenceHallID = ResidenceHall.residenceHallID

AND Inspections.inspectionScore = 'Pass') AS TotalPasses

FROM Inspections

JOIN Rooms ON Inspections.roomID = Rooms.roomID

JOIN DormFloor ON Rooms.DormFloor_floorID = DormFloor.floorID

JOIN ResidenceHall ON DormFloor.residenceHallID = ResidenceHall.residenceHallID

GROUP BY ResidenceHall.address, ResidenceHall.residenceHallID

ORDER BY TotalPasses DESC;

<img width="795" height="176" alt="image" src="https://github.com/user-attachments/assets/26c63ebe-b31b-4fdd-b66d-382ef4347c58" />

Query 2 allows managers to see how many inspections each residence hall has had, along with how many of those inspections passed. This helps managers identify which buildings are performing well during inspections and which may require improvements. Ordering the results by the number of passes allows managers to quickly identify the most compliant residence halls and prioritize those that need additional attention or maintenance.

# Query 3
Query 3 - StudentParking

SELECT ResidenceHall.address, COUNT(Students.studentID) AS TotalStudents,
(COUNT(Students.studentID)-(SELECT COUNT(Parking.studentID) FROM Parking

JOIN Students ON Parking.studentID = Students.studentID

JOIN DormFloor ON Students.floorID = DormFloor.floorID

WHERE DormFloor.residenceHallID = ResidenceHall.residenceHallID)) AS StudentsWithoutParking

FROM ResidenceHall

JOIN DormFloor ON ResidenceHall.residenceHallID = DormFloor.residenceHallID

JOIN Students ON Students.floorID = DormFloor.floorID

GROUP BY ResidenceHall.residenceHallID, ResidenceHall.address

ORDER BY StudentsWithoutParking DESC;

<img width="790" height="215" alt="image" src="https://github.com/user-attachments/assets/7d47044b-5154-4dcc-af5f-f7705594e43c" />

Query 3 allows managers to compare the total number of students in each residence hall to the number of those students who have parking spaces. This provides insight into how many students live in each hall without access to parking, which can help determine if additional parking spaces are needed. If a hall shows a large number of students without parking, it may indicate a need to expand parking options or provide alternative transportation services.

# Query 4
Query 4 - RAAssignments

WITH ra_boss AS (SELECT DISTINCT studentID, studentName FROM Students

WHERE studentID IN (SELECT DISTINCT raID FROM Students)), ra_worker AS (SELECT * FROM Students)

SELECT ra_boss.studentName AS "RA", ra_worker.studentName AS "Student"

FROM ra_boss JOIN ra_worker ON ra_worker.raID = ra_boss.studentID

ORDER BY ra_boss.studentName, ra_worker.studentName;

<img width="665" height="295" alt="image" src="https://github.com/user-attachments/assets/034a232f-5d23-4bb4-87cd-973be7f5831f" />

Query 4 allows managers to see which students are Resident Assistants (RAs) and which students are under their supervision. This helps managers visualize the hierarchical structure of RAs and their assigned residents, ensuring that each student is appropriately paired with an RA. Organizing the results by RA name makes it easier to see who manages which students and confirm that all residents are accounted for.

# Query 5
Query 5 - StudentMaintenanceRequests

SELECT Students.studentName, Rooms.roomID, MaintainanceRequests.requestID FROM Students

JOIN DormFloor ON Students.floorID = DormFloor.floorID

JOIN Rooms ON DormFloor.floorID = Rooms.DormFloor_floorID

JOIN MaintainanceRequests ON Rooms.roomID = MaintainanceRequests.roomID

ORDER BY Students.studentName, MaintainanceRequests.requestID;

<img width="687" height="272" alt="image" src="https://github.com/user-attachments/assets/a93b9c53-2876-4953-957e-a80c8a4fd65a" />

Query 5 allows managers to identify which students have maintenance requests connected to their rooms. This can be used to track which rooms are experiencing issues and which students are affected. By listing students alongside their room IDs and maintenance request IDs, managers can easily follow up with the responsible staff and ensure that requests are resolved promptly.

# Query 6
Query 6 - StaffRequests

SELECT UGAstaff.staffName, ResidenceHall.address, COUNT(MaintainanceRequests.requestID) AS NumRequests FROM MaintainanceRequests

JOIN UGAstaff ON MaintainanceRequests.staffID = UGAstaff.staffID

JOIN Rooms ON MaintainanceRequests.roomID = Rooms.roomID

JOIN DormFloor ON Rooms.DormFloor_floorID = DormFloor.floorID

JOIN ResidenceHall ON DormFloor.residenceHallID = ResidenceHall.residenceHallID

GROUP BY UGAstaff.staffName, ResidenceHall.address

ORDER BY UGAstaff.staffName, ResidenceHall.address;

<img width="665" height="272" alt="image" src="https://github.com/user-attachments/assets/32a5235f-9788-4f52-ae60-9dbed27eca32" />

Query 6 allows managers to see which staff members have handled the most maintenance requests in each residence hall. This helps identify which staff are most active or which buildings require the most staff attention. Managers can use this information to evaluate workload distribution, recognize high-performing employees, and ensure that maintenance staff are allocated effectively across all halls.

# Query 7
Query 7 - GetAllRequests

SELECT MaintainanceRequests.requestID, MaintainanceRequests.roomID, UGAstaff.staffName, UGAstaff.staffPhone

FROM MaintainanceRequests

JOIN UGAstaff ON MaintainanceRequests.staffID = UGAstaff.staffID;

<img width="778" height="285" alt="image" src="https://github.com/user-attachments/assets/ee92cc7c-44d1-4384-8fcf-baaa8242a19a" />

Query 7 allows managers to view all maintenance requests along with the staff member responsible for each one. This provides an easy way to match staff names and contact information to specific requests, allowing managers to follow up on progress or assign new tasks efficiently.

# Query 8
Query 8 - StudentsWithParking

SELECT studentName,
(SELECT lotLocation FROM Parking 
WHERE Parking.studentID = Students.studentID) AS lotLocation

FROM Students

WHERE EXISTS (SELECT 1 FROM Parking

WHERE Parking.studentID = Students.studentID);

<img width="666" height="257" alt="image" src="https://github.com/user-attachments/assets/b83c5f12-4992-43e8-a150-56801c9b70fa" />

Query 8 allows managers to see which students have parking spaces and where those spaces are located. This can help the management team confirm that parking assignments are accurate and up to date. It also ensures that only students who actually have parking spots appear in the results, simplifying record verification.

# Query 9
Query 9 - RoomLocations

SELECT Rooms.roomID, DormFloor.floorNumber, ResidenceHall.address

FROM Rooms

JOIN DormFloor ON Rooms.DormFloor_floorID = DormFloor.floorID

JOIN ResidenceHall ON DormFloor.residenceHallID = ResidenceHall.residenceHallID;

<img width="781" height="290" alt="image" src="https://github.com/user-attachments/assets/69625340-a25d-486e-a09d-1aa6c9082eb7" />

Query 9 allows managers to view which rooms belong to which floors and which residence halls. This provides a clear understanding of the building layout and structure. It helps management easily identify where each room is located, which is useful for maintenance, inspections, or housing assignments.

# Query 10
Query 10 - StudentRooms

SELECT Students.studentName, Rooms.roomID FROM Students

JOIN DormFloor ON Students.floorID = DormFloor.floorID

JOIN Rooms ON DormFloor.floorID = Rooms.DormFloor_floorID;

<img width="776" height="232" alt="image" src="https://github.com/user-attachments/assets/597e04ef-addb-401b-b555-d547ccfaf04a" />

Query 10 allows managers to see which room each student is associated with. This helps confirm that every student is correctly assigned to a room and provides an organized way to verify housing records. It also allows managers to identify which floors and rooms are occupied, assisting with housing capacity planning.

## Database Information
Name of the database: ns_F25MIST4610_59925_Group3

Additional information: Each query listed above is marked in the database using stored procedures which can be called.

