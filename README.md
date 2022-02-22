# Hospital Management System 

<div style="padding:20px;">
<p>We as a team of 5 students gathered a huge variety of information from the real-world documents by researching. Then we created our own document with some fake personas to imitate a functioning hospital. After that, we pinpointed the attributes that has a potential of having primary/secondary keys. From that we formed a 3NF relational database from scratch using necessary sql queries that we wrote on MySQL server.</p>
<br></div>
<center>

## The E-R Diagram of our Database

<img src="img\Project ER Diagram.jpeg">

</center>
<div style="padding:30px">

# Samples of the SQL Queries
1. [Creating the UNF table](#creating-the-unf-table)
2. [Forming 3NF tables from the UNF table](#forming-3nf-tables-from-the-unf-table)
3. [Creating view of Patient Records by joining every table](#creating-view-of-patient-records-by-joining-every-table)

</div>

<div style="margin:20px; padding:20px;">

## Creating the UNF table
<br>
<div style="overflow: auto; padding:10px; height:350px; text-align:center;">
    <div style="width:600px;display:inline-block;text-align:left;">
        
   ``` sql
    CREATE TABLE Hospital_Patient_Record (
    patient_tc_no VARCHAR(11) NOT NULL,
    patient_first_name VARCHAR(30) NOT NULL,
    patient_last_name VARCHAR(30) NOT NULL,
    patient_dob date NOT NULL,
    patient_sex VARCHAR(6) NOT NULL,
    patient_mail VARCHAR(45) DEFAULT NULL,
    patient_phone VARCHAR(10) DEFAULT NULL,
    patient_address_building_no VARCHAR(20) NOT NULL,
    patient_address_street VARCHAR(20) NOT NULL,
    patient_address_neighbourhood VARCHAR(20) NOT NULL,
    patient_address_district VARCHAR(20) NOT NULL,
    patient_blood VARCHAR(3) NOT NULL,
    patient_allergies VARCHAR(50) DEFAULT 'no allergies',
    doctor_id INT NOT NULL,
    doctor_first_name VARCHAR(30) NOT NULL,
    doctor_last_name VARCHAR(30) NOT NULL,
    doctor_sex VARCHAR(6) NOT NULL,
    doctor_phone VARCHAR(10) DEFAULT NULL,
    doctor_mail VARCHAR(45) DEFAULT NULL,
    doctor_clinic VARCHAR(30) NOT NULL,
    doctor_working_days VARCHAR(9) NOT NULL,
    nurse_id INT DEFAULT NULL,
    nurse_first_name VARCHAR(30) DEFAULT NULL,
    nurse_last_name VARCHAR(30) DEFAULT NULL,
    nurse_sex VARCHAR(6) DEFAULT NULL,
    nurse_phone VARCHAR(10) DEFAULT NULL,
    nurse_mail VARCHAR(45) DEFAULT NULL,
    nurse_clinic VARCHAR(30) DEFAULT NULL,
    diagnosed_disease VARCHAR(30) DEFAULT NULL,
    doctor_comment TEXT DEFAULT NULL,
    recommended_medicine VARCHAR(30) DEFAULT NULL,
    allergenic_ingridient VARCHAR(30) DEFAULT NULL,
    check_date DATETIME DEFAULT NULL,
    rest_room_block VARCHAR(5) DEFAULT NULL,
    rest_room_floor VARCHAR(3) DEFAULT NULL,
    rest_room_no VARCHAR(5) DEFAULT NULL,
    analysis_type VARCHAR(30) DEFAULT NULL,
    analysis_result TEXT DEFAULT NULL,
    next_appointment DATETIME DEFAULT NULL
    );

    INSERT INTO `hospital_patient_record` (
		`patient_tc_no`, 
		`patient_first_name`, 
		`patient_last_name`, 	
		`patient_dob`, 
		`patient_sex`,
	 	`patient_mail`, 
		`patient_phone`, 
    	`patient_address_building_no`,
    	`patient_address_street`,
    	`patient_address_neighbourhood`,
    	`patient_address_district`,
		`patient_blood`, 
		`patient_allergies`, 
		`doctor_id`, 
		`doctor_first_name`, 
		`doctor_last_name`, 
		`doctor_sex`, 
		`doctor_phone`, 
		`doctor_mail`, 
		`doctor_clinic`, 
		`doctor_working_days`,
		`nurse_id`, 
		`nurse_first_name`, 
		`nurse_last_name`, 
		`nurse_sex`, 
		`nurse_phone`, 
		`nurse_mail`, 
		`nurse_clinic`, 
		`diagnosed_disease`, 
		`doctor_comment`, 
		`recommended_medicine`, 
    	`allergenic_ingridient`,
		`check_date`, 
        `rest_room_block`,
        `rest_room_floor`,
        `rest_room_no`,
		`analysis_type`, 
		`analysis_result`,
		`next_appointment`) 
        
        VALUES 
        
		('12345678912',
		 'murat',
		 'pasa',
		 '1956-03-15',
		 'male',
		 DEFAULT,
		 '5323323232',
		 '12',
		 'deniz',
		 'lara',
		 'muratpasa',
		 'ab+',
		 'aspirin',
		 '1119',
		 'Murat',
		 'kınıkoğlu',
		 'male',
		 '5112112121',
		 'muratkinikoglu@gmail.com',
		 'oncology',
		 'tuesday',
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		  DEFAULT,
		 '2021-01-12 14:10:00'
)
```

</div>
</div>
<div style="padding:50px;"><p>
You can see rest of the necessary queries for creating the single table and inserting the values that we created here!
</div><p>

## Forming 3NF tables from the UNF table
<br>
<div style="overflow: auto; padding:10px; height:350px; text-align:center;">
    <div style="width:600px;display:inline-block;text-align:left;">
        
   ``` sql

######################		PATIENT

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

######################   ALLERGY

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

######################		DOCTOR

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

######################   APPOINTMENT

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

######################   NURSE

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

###################### 	 DISEASE

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

######################	ANALYSIS TYPE

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
```

</div>

</div>

<div style="padding:50px;">

<p>
You can see rest of the necessary queries for separating the main table into smaller group of attributes with their primary/secondary keys and the placing ID to take the place of those attributes here!

<p>

</div>


## Creating view of Patient Records by joining every table
<br>
<div style="overflow: auto; padding:10px; height:350px; text-align:center;">
    <div style="width:600px;display:inline-block;text-align:left;">
        
   ``` sql
    SELECT 
    p.tc_no AS patient_tc_no,
    p.first_name AS patient_first_name,
    p.last_name AS patient_last_name,
    p.dob AS patient_dob,
    p.sex AS patient_sex,
    p.mail AS patient_mail,
    p.phone AS patient_phone,
    sb.buildingNo AS patient_address_building_no,
    sb.streetName AS patient_address_street,
    n.name AS patient_address_neighbourhood,
    dis.name AS patient_address_district,
    at.name AS patient_allergies,
    p.blood AS patient_blood,
    d.ID AS doctor_id,
    d.first_name AS doctor_first_name,
    d.last_name AS doctor_last_name,
    d.sex AS doctor_sex,
    d.phone AS doctor_phone,
    d.mail AS doctor_mail,
    dcl.name AS doctor_clinic,
    days.day AS doctor_working_days,
    NULL AS nurse_id,
    NULL AS nurse_first_name,
    NULL AS nurse_last_name,
    NULL AS nurse_sex,
    NULL AS nurse_phone,
    NULL AS nurse_mail,
    NULL AS nurse_clinic,
    NULL AS diagnosed_disease, 
    NULL AS doctor_comment,
    NULL AS recommended_medicine,
    NULL AS allergenic_ingridient,
    NULL AS check_date,
    NULL AS rest_room_block,
    NULL AS rest_room_floor,
    NULL AS rest_room_no,
    NULL AS analysis_type,
    NULL AS analysis_result,app.appDate AS next_appointment
       FROM appointment AS app
    INNER JOIN patient AS p
       ON p.tc_no = app.patientTC
    INNER JOIN patientallergies AS pa
       ON pa.patientTC = p.tc_no
    INNER JOIN allergytype AS at
       ON at.ID = pa.allergyID
    INNER JOIN streetbuilding AS sb
       ON sb.patientTC = p.tc_no
    INNER JOIN neighbourhood AS n
    	ON n.ID = sb.neighbourhoodID
    INNER JOIN district AS dis
       ON dis.ID = n.districtID
    INNER JOIN doctor AS d
       ON d.ID = app.doctorID
    INNER JOIN shift AS sh
       ON sh.doctorID = d.ID
    INNER JOIN days
       ON days.ID = sh.dayID
    INNER JOIN clinic AS dcl
       ON dcl.ID = d.clinicID 
    WHERE app.appDate NOT IN (SELECT next_appointment FROM treatmentrecord WHERE next_appointment IS NOT NULL)
    
    UNION
 
    SELECT
    p.tc_no,
    p.first_name,
    p.last_name,
    p.dob,
    p.sex,
    p.mail,
    p.phone,
    p.blood,
    sb.buildingNo,
    sb.streetName,
    n.name,
    dis.name,
    at.name,
    d.ID,
    d.first_name,
    d.last_name,
    d.sex,
    d.phone,d.mail,
    dcl.name,
    days.day,
    nur.ID,
    nur.first_name,
    nur.last_name,
    nur.sex,
    nur.phone,
    nur.mail,
    ncl.name, 
    disease.name,
    tr.doctorComment,
    m.name,
    atm.name,
    tr.treatmentTime,
    block.letter,
    rr.floor,
    rr.roomno,
    anlt.title,
    anl.result,
    tr.next_appointment
       FROM treatmentrecord AS tr
    INNER JOIN patient AS p
       ON p.tc_no = tr.patientTC
    INNER JOIN patientallergies AS pa
       ON pa.patientTC = p.tc_no
    INNER JOIN allergytype AS at
       ON at.ID = pa.allergyID
    INNER JOIN streetbuilding AS sb
       ON sb.patientTC = p.tc_no
    INNER JOIN neighbourhood AS n
    	ON n.ID = sb.neighbourhoodID
    INNER JOIN district AS dis
       ON dis.ID = n.districtID
    INNER JOIN doctor AS d
       ON d.ID = tr.doctorID
    INNER JOIN shift AS sh
       ON sh.doctorID = d.ID
    INNER JOIN days
       ON days.ID = sh.dayID
    INNER JOIN nurse AS nur
       ON nur.ID = tr.nurseID
    INNER JOIN clinic AS dcl
       ON dcl.ID = d.clinicID 
    INNER JOIN clinic AS ncl
       ON ncl.ID = nur.clinicID
    INNER JOIN recommended_med AS rm
       ON rm.patientTC = tr.patientTC
      AND rm.prescribed_date = tr.treatmentTime
    INNER JOIN medicine AS m 
       ON m.ID = rm.medicineID
     LEFT JOIN allergenicingridient AS alin
       ON alin.medicineID = m.ID
     LEFT JOIN allergytype AS atm
       ON atm.ID = alin.allergyID
     LEFT JOIN restroom AS rr
       ON rr.patientTC = tr.patientTC
     LEFT JOIN block 
       ON rr.blockID = block.ID
     LEFT JOIN analysis AS anl
       ON anl.treatmentID = tr.ID
     LEFT JOIN analysistype anlt
       ON anlt.ID = anl.typeID
    INNER JOIN diagnoseddisease AS dd
       ON dd.treatmentID = tr.ID
    INNER JOIN disease
       ON disease.ID = dd.diseaseID
```

</div>

</div>

<div style="padding:50px;">

<p>

In the SQL code above, there is union of two select queries full of necessary join commands to form such a big view of hospital records which is the copy of the original table.
</div>

<p>

</div>