
## Forming 3NF tables from the UNF table

<br>
You can see all of the necessary queries for separating the main table into multiple tables with a smaller group of attributes and their primary/secondary keys and the placing ID to take the place of those attributes here!
<br><br>
        
   ``` sql

###############################			PATIENT

#Creating the patient table with tc_no as primary key
CREATE TABLE Patient (
    tc_no VARCHAR(11) NOT NULL,
    first_name VARCHAR(30) NOT NULL,
    last_name VARCHAR(30) NOT NULL,
    dob date NOT NULL,
    sex VARCHAR(6) NOT NULL,
    mail VARCHAR(45) DEFAULT NULL,
    phone VARCHAR(10) DEFAULT NULL,
    address_building_no VARCHAR(20) NOT NULL,
    address_street VARCHAR(20) NOT NULL,
    address_neighbourhood VARCHAR(20) NOT NULL,
    address_district VARCHAR(20) NOT NULL,
    blood VARCHAR(3) NOT NULL,
    PRIMARY KEY(tc_no));

INSERT INTO patient (tc_no,first_name,last_name,dob,sex,mail,phone,address_building_no,address_street,address_neighbourhood,address_district,blood)
SELECT DISTINCT patient_tc_no, patient_first_name,patient_last_name, patient_dob,patient_sex,patient_mail,patient_phone,patient_address_building_no,patient_address_street,patient_address_neighbourhood,patient_address_district,patient_blood
           FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
DROP COLUMN patient_first_name, 
DROP COLUMN patient_last_name,
DROP COLUMN patient_dob,
DROP COLUMN patient_sex,
DROP COLUMN patient_mail,
DROP COLUMN patient_phone,
DROP COLUMN patient_address_building_no,
DROP COLUMN patient_address_street,
DROP COLUMN patient_address_neighbourhood,
DROP COLUMN patient_address_district,
DROP COLUMN patient_blood;

###################################		ALLERGY

CREATE TABLE allergyType(
	ID INT NOT NULL AUTO_INCREMENT,
	name VARCHAR(40),
	PRIMARY KEY(ID)
);

INSERT INTO allergyType (name) 
SELECT DISTINCT LOWER(patient_allergies) FROM hospital_patient_record  WHERE patient_allergies IS NOT NULL;

CREATE TABLE patientAllergies(
	patientTC VARCHAR(11) NOT NULL,
	allergyID INT NOT NULL,
	FOREIGN KEY(patientTC) REFERENCES patient(tc_no),
	FOREIGN KEY(allergyID) REFERENCES allergyType(id)
);	

INSERT INTO patientAllergies(patientTC,allergyID)
SELECT DISTINCT h.patient_tc_no,a.ID
FROM hospital_patient_record AS h
INNER JOIN allergytype AS a
WHERE h.patient_allergies = a.name;

ALTER TABLE hospital_patient_record
 DROP patient_allergies;

UPDATE allergyType
   SET name = 'no allergies'
 WHERE name = '';

###############################			DOCTOR

CREATE TABLE Doctor (
    ID INT NOT NULL,
    first_name VARCHAR(30) NOT NULL,
    last_name VARCHAR(30) NOT NULL,
    sex VARCHAR(6) NOT NULL,
    phone VARCHAR(10) DEFAULT NULL,
    mail VARCHAR(45) DEFAULT NULL,
    clinic VARCHAR(30) NOT NULL,
    PRIMARY KEY(id));

INSERT INTO doctor (id,first_name,last_name,sex,phone,mail,clinic)
SELECT DISTINCT doctor_id,LOWER(doctor_first_name),LOWER(doctor_last_name), LOWER(doctor_sex),doctor_phone,doctor_mail,LOWER(doctor_clinic)
           FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
DROP COLUMN doctor_first_name,
DROP COLUMN doctor_last_name,
DROP COLUMN doctor_sex,
DROP COLUMN doctor_phone,
DROP COLUMN doctor_mail,
DROP COLUMN doctor_clinic;

#############################			APPOINTMENT

CREATE TABLE appointment (
	ID INT AUTO_INCREMENT,
	patientTC VARCHAR(11) NOT NULL,
	doctorID INT NOT NULL,
	appDate DATETIME NOT NULL,
	PRIMARY KEY(ID),
	FOREIGN KEY (patientTC) REFERENCES Patient(tc_no),
	FOREIGN KEY (doctorID) REFERENCES Doctor(ID));

INSERT INTO appointment (patientTC,doctorID,appDate)
SELECT DISTINCT patient_tc_no,doctor_id,next_appointment
  FROM hospital_patient_record
 WHERE next_appointment !=0
   AND next_appointment > DATE(NOW());

DELETE FROM hospital_patient_record
WHERE next_appointment  !=0
AND diagnosed_disease = '';

ALTER TABLE hospital_patient_record
DROP COLUMN next_appointment;

###############################			NURSE

CREATE TABLE Nurse (
    ID INT NOT NULL,
    first_name VARCHAR(30) NOT NULL,
    last_name VARCHAR(30) NOT NULL,
    sex VARCHAR(6) NOT NULL,
    phone VARCHAR(10) DEFAULT NULL,
    mail VARCHAR(45) DEFAULT NULL,
    clinic VARCHAR(30) NOT NULL,
    PRIMARY KEY(id));

#I am putting all the values of nurse into the nurse table
INSERT INTO nurse (id,first_name,last_name,sex,phone,mail,clinic)
SELECT DISTINCT nurse_id,LOWER(nurse_first_name), LOWER(nurse_last_name), LOWER(nurse_sex),nurse_phone,nurse_mail,nurse_clinic
           FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
DROP COLUMN nurse_first_name,
DROP COLUMN nurse_last_name,
DROP COLUMN nurse_sex,
DROP COLUMN nurse_phone,
DROP COLUMN nurse_mail,
DROP COLUMN nurse_clinic;

#################################		DISEASE

CREATE TABLE Disease (
    ID INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(30),
    PRIMARY KEY (id)
);

#I know mysql is not case sensitive but using lower makes the datas in the table seem less distracting
INSERT INTO disease (name)
SELECT DISTINCT LOWER(diagnosed_disease) FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
ADD COLUMN diseaseID INT NOT NULL;

#Now it is time to match the disease IDs to the right patients
UPDATE hospital_patient_record AS h INNER JOIN disease 
SET diseaseID = disease.ID WHERE h.diagnosed_disease = disease.name;

ALTER TABLE hospital_patient_record DROP diagnosed_disease;

###################################		ANALYSIS TYPE

CREATE TABLE analysisType(
    ID INT NOT NULL AUTO_INCREMENT,
    title VARCHAR(30) NOT NULL,
    PRIMARY KEY(id)
);
INSERT INTO analysisType (title)
SELECT DISTINCT LOWER(analysis_type) FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
ADD COLUMN analysisType INT NOT NULL;

UPDATE hospital_patient_record AS h INNER JOIN analysisType AS aType 
SET h.analysisType = aType.ID
WHERE h.analysis_type = aType.title;

ALTER TABLE hospital_patient_record
DROP analysis_type;

UPDATE analysisType
   SET title = 'not analysed'
 WHERE title = '';

###################################		ANALYSIS

CREATE TABLE analysis(
    ID INT NOT NULL AUTO_INCREMENT,
    typeID INT NOT NULL,
    result TEXT,
    PRIMARY KEY(id),
    FOREIGN KEY(typeID) REFERENCES analysisType(ID)
);

INSERT INTO analysis (result,typeID)
SELECT DISTINCT analysis_result,analysisType FROM hospital_patient_record;

ALTER TABLE hospital_patient_record
ADD COLUMN analysisID INT NOT NULL;

UPDATE hospital_patient_record AS h INNER JOIN analysis AS a
SET h.analysisID = a.ID
WHERE h.analysis_result = a.result;

ALTER TABLE hospital_patient_record
DROP analysis_result,
DROP analysistype;

###################################		MEDICINE

CREATE TABLE medicine(
    ID INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(30),
    PRIMARY KEY (ID)
);

INSERT INTO medicine(name)
SELECT DISTINCT LOWER(recommended_medicine) 
  FROM hospital_patient_record 
 WHERE recommended_medicine IS NOT NULL;

ALTER TABLE hospital_patient_record
  ADD COLUMN medicineID INT NOT NULL;

UPDATE hospital_patient_record AS h INNER JOIN medicine AS m
   SET h.medicineID = m.ID
 WHERE h.recommended_medicine = m.name;

CREATE TABLE recommended_med(
    ID INT AUTO_INCREMENT,
    patientTC VARCHAR(11) NOT NULL,
    medicineID INT NOT NULL,
    prescribed_date DATETIME,
    PRIMARY KEY(ID),
    FOREIGN KEY(patientTC) REFERENCES patient(tc_no),
    FOREIGN KEY(medicineID) REFERENCES medicine(ID)
);

UPDATE medicine
   SET name = 'not given'
 WHERE name = '';

INSERT INTO recommended_med(patientTC,medicineID,prescribed_date)
SELECT DISTINCT h.patient_tc_no,h.medicineID,h.check_date
FROM hospital_patient_record AS h
WHERE h.medicineID != (SELECT ID FROM medicine AS m WHERE m.name = 'not given');

ALTER TABLE hospital_patient_record
 DROP recommended_medicine;

###################################		ASSIGNED

CREATE TABLE assigned(
    ID INT NOT NULL AUTO_INCREMENT,
    doctorID INT NOT NULL,
    nurseID INT,
    checkTime DATETIME NOT NULL,
    PRIMARY KEY (ID,checkTime),
    FOREIGN KEY(doctorID) REFERENCES doctor(ID),
    FOREIGN KEY(nurseID) REFERENCES nurse(ID)
);
    
INSERT INTO assigned(doctorID,nurseID,checkTime)
SELECT DISTINCT doctor_id,nurse_id,check_date FROM hospital_patient_record;
    
ALTER TABLE hospital_patient_record
  ADD COLUMN assignedID INT NOT NULL;

UPDATE hospital_patient_record AS h INNER JOIN assigned AS a 
   SET h.assignedID = a.ID
 WHERE h.check_date = a.checkTime;
    
ALTER TABLE hospital_patient_record
 DROP check_date,
 DROP doctor_id,
 DROP nurse_id;

##################################		CLINIC

CREATE TABLE clinic(
    ID INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(30),
    PRIMARY KEY(ID)
    );

INSERT INTO clinic(name)
SELECT DISTINCT clinic FROM doctor;

ALTER TABLE doctor 
  ADD COLUMN clinicID INT NOT NULL;

UPDATE doctor AS d INNER JOIN clinic as c
   SET d.clinicID = c.ID
 WHERE d.clinic = c.name;

ALTER TABLE `doctor` ADD CONSTRAINT `clinicID` FOREIGN KEY (`clinicID`) REFERENCES `clinic`(`ID`) ON DELETE RESTRICT ON UPDATE RESTRICT;

ALTER TABLE doctor
 DROP clinic;

###########################		TREATMENT RECORD

CREATE TABLE treatmentRecord(
	patientTC VARCHAR(11) NOT NULL,
	assignedID INT NOT NULL,
	diseaseID INT NOT NULL,
	analysisID INT NOT NULL,
	doctorComment TEXT,
	FOREIGN KEY(patientTC) REFERENCES patient(tc_no),
	FOREIGN KEY(assignedID) REFERENCES assigned(ID),
	FOREIGN KEY(diseaseID) REFERENCES disease(ID),
	FOREIGN KEY(analysisID) REFERENCES analysis(ID)
);

INSERT INTO treatmentRecord(patientTC,assignedID,diseaseID,analysisID,doctorComment)
SELECT DISTINCT patient_tc_no,assignedID,diseaseID,analysisID,doctor_comment
FROM hospital_patient_record;

###################################		DISTRICT

CREATE TABLE district(
	ID INT NOT NULL AUTO_INCREMENT,
	name VARCHAR(20),
	PRIMARY KEY(ID)
);

INSERT INTO district(name)
SELECT DISTINCT address_district
  FROM patient;

###################################		NEIGHBOURHOOD

CREATE TABLE neighbourhood(
    ID INT NOT NULL AUTO_INCREMENT,
    districtID INT NOT NULL,
    name VARCHAR(30),
    PRIMARY KEY(ID),
    FOREIGN KEY(districtID) REFERENCES district(ID)
    );

INSERT INTO neighbourhood(name,districtID)
SELECT DISTINCT p.address_neighbourhood, d.ID
  FROM patient AS p
 INNER JOIN district AS d
    ON d.name = p.address_district;

###################################		STREET & BUILDING

CREATE TABLE streetBuilding(
    ID INT NOT NULL AUTO_INCREMENT,
    neighbourhoodID INT NOT NULL,
    streetName VARCHAR(20),
    buildingNo VARCHAR(20),
    PRIMARY KEY(ID),
    FOREIGN KEY(neighbourhoodID) REFERENCES neighbourhood(ID)
    );

INSERT INTO streetBuilding(streetName,buildingNo,neighbourhoodID)
SELECT DISTINCT p.address_street, p.address_building_no,n.ID
  FROM patient AS p
 INNER JOIN neighbourhood AS n
    ON n.name = p.address_neighbourhood;

ALTER TABLE patient 
  ADD COLUMN addressID INT;
  
UPDATE patient AS p INNER JOIN streetbuilding AS sb 
   SET p.addressID = sb.ID 
 WHERE p.address_building_no = sb.buildingNo
   AND p.address_street = sb.streetName;

ALTER TABLE patient
 DROP COLUMN address_building_no,
 DROP COLUMN address_street,
 DROP COLUMN address_neighbourhood,
 DROP COLUMN address_district;

ALTER TABLE `patient` ADD CONSTRAINT `addressID` FOREIGN KEY (`addressID`) REFERENCES `streetBuilding`(`ID`) ON DELETE RESTRICT ON UPDATE RESTRICT;

###########################		BLOCK

DELETE FROM hospital_patient_record
 WHERE rest_room_block = ''
   AND rest_room_floor = ''
   AND rest_room_no = '';

CREATE TABLE block(
	ID INT AUTO_INCREMENT,
	letter VARCHAR(3),
	PRIMARY KEY(ID)
);

INSERT INTO block(letter)
SELECT DISTINCT rest_room_block
FROM hospital_patient_record
ORDER BY rest_room_block;

###########################		FLOOR&NO

CREATE TABLE restroom(
    ID INT AUTO_INCREMENT,
    roomno INT NOT NULL,
    floor INT NOT NULL,
    blockID INT NOT NULL,
    patientTC VARCHAR(11),
    PRIMARY KEY(ID),
    FOREIGN KEY(patientTC) REFERENCES patient(tc_no),
    FOREIGN KEY(blockID) REFERENCES block(ID)
    );

INSERT INTO restroom(roomno,floor,blockID,patientTC)
SELECT h.rest_room_no,h.rest_room_floor,b.ID,h.patient_tc_no
  FROM hospital_patient_record AS h
 INNER JOIN block AS b
    ON b.letter = h.rest_room_block
 ORDER BY b.ID;

DROP TABLE hospital_patient_record;

```
