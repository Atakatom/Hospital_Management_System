## Creating view of Patient Records by joining every table

<br>
In the SQL code below, there is union of two select queries full of necessary join commands to form such a big view of hospital records which is the copy of the original table.
<br><br>

        
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
