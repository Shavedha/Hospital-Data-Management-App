# HOSPITAL DATA MANAGEMENT APP USING REACT, SPRINGBOOT AND SQL
## AIM
To create an Hospital-Data-Management-App using React,SpringBoot and SQL.
## ALGORITHM
1. Configure a new project with the required dependencies and files.
2. Initialise the same in IntelliJ and include file Patient.java which contains all the variables like Patient ID,name,DOB,Email and their respective constructor,getters and setters.
3. Include Components and service files which configures and works for commands like GET,POST and DELETE.
4. Run the file by connecting to an database by creating the same in psql.
5. Start an react project named Hospital-data-management-frontend
6. Include necessary functions in App.js
7. Include the necessary .css files for all the three methods.
8. Define separate .js files for viewing the list of patients,adding an patient,and deleting an patient.
9. Connect with the backend server and view the output user interface in the browser.

## PROGRAM
### SpringBoot Program
### Patient.java
```
package com.saveetha.patient.hos;
import jakarta.persistence.*;
import java.time.LocalDate;
import java.time.Period;

@Entity
@Table
public class Patient{
    @Id
    @SequenceGenerator(
            name="patient_sequence",
            sequenceName = "patient_sequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "patient_sequence"
    )
   private Long PatientID;
    private String PatientName;

    @Transient
    private int PatientAge;
    private LocalDate PatientDOB;
    private String DiseaseIdentified;
    public Patient(){

    }

    public Patient(Long PatientID, String PatientName,  LocalDate PatientDOB, String DiseaseIdentified) {
        this.PatientID = PatientID;
        this.PatientName = PatientName;

        this.PatientDOB = PatientDOB;
        this.DiseaseIdentified = DiseaseIdentified;
    }

    public Long getPatientID() {
        return PatientID;
    }

    public String getPatientName() {
        return PatientName;
    }

    public int getPatientAge() {

        return Period.between(PatientDOB, LocalDate.now()).getYears();
    }

    public LocalDate getPatientDOB() {
        return PatientDOB;
    }

    public String getDiseaseIdentified() {
        return DiseaseIdentified;
    }

    public void setPatientID(Long PatientID) {
        this.PatientID = PatientID;
    }

    public void setPatientName(String PatientName) {
        this.PatientName = PatientName;
    }

    public void setPatientAge(int PatientAge) {
        this.PatientAge = PatientAge;
    }

    public void setPatientDOB(LocalDate PatientDOB) {
        this.PatientDOB = PatientDOB;
    }

    public void setDiseaseIdentified(String DiseaseIdentified) {
        this.DiseaseIdentified = DiseaseIdentified;
    }

    @Override
    public String toString() {
        return "Patient{" +
                "PatientID=" + PatientID +
                ", PatientName='" + PatientName + '\'' +
                ", PatientAge=" + PatientAge +
                ", PatientDOB=" + PatientDOB +
                ", DiseaseIdentified='" + DiseaseIdentified+ '\'' +
                '}';
    }
}
```
### PatientController.java
```
package com.saveetha.Patient.hos;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDate;
import java.time.Month;
import java.util.List;

@RestController
@RequestMapping(path="/api/v1/Patient")
public class PatientController {
    private final PatientService patientService;

    @Autowired
    public PatientController(PatientService patientService) {
        this.PatientService = patientService;
    }

    @GetMapping("/")
    public List<Patient> getPatientDetails() {

       return patientService.displayPatientDetails();
    }
    @PostMapping("/")
    public void postEmployee(@RequestBody Patient patient){
        PatientService.registerNewPatient(patient);
    }
    @DeleteMapping(path = {"/{id}"})
    public void deletePatient(@PathVariable("id") Long PatientId){
        PatientService.removePatient(PatientId);
    }
}
```
### React Program
### PatientDirectoryComponent.js
```
// 
import React, { useEffect, useState } from 'react'
import './PatientDirectoryComponent.css'

function PatientDirectoryComponent() {
    const [patient,setPatient] = useState([])

    useEffect(() => {
        fetchpatient()
    }, [])

    const fetchpatient = async() => {
        try{
        const response = await fetch('http://localhost:8080/api/v1/Patient/')
        const data = await response.json()
        setPatient(data)
        }
        catch(error){
            console.log("Error:",error);
        }
    }
  return (
    <div className='Patient-list'>
        {patient.map((patient) =>(
            <div className='Patient-card' key={patient.PatientID}>
                <p>Patient ID: {patient.PatientID}</p>
                <p> Name: {patient.PatientName}</p>
                <p> Age: {patient.PatientAge}</p>
                <p> DOB: {patient.PatientDOB}</p>
                <p> DiseaseIdentified: {patient.DiseaseIdentified}</p>
            </div>
        )
        )}
    </div>
  )
}

export default PatientDirectoryComponent
```
### PatientRegistrationComponent
```
import React,{useState} from 'react'
import './PatientRegistrationComponent.css'
function PatientRegistrationComponent() {

    const [patient,setPatient] = useState({
        PatientName: '',
        PatientDOB: '',
        PatientEmail: ''
    })

    const handleChange = (event) => {
        const {name,value} = event.target
        setPatient({...Patient,[name]:value})
    }

    const handleFormSubmit = async(event) => {
        event.preventDefault();
        await fetch('http://localhost:8080/api/v1/Patient/',{
            method:'POST',
            headers:{
                'Content-Type':'application/json'
            },
            body:JSON.stringify(patient)
        })
        .then((response) => {
            if (response.status == 500){
                alert('Error!')
               
            }
            else{
                alert(`Data of ${patient.PatientName} is added successfully`)
                window.location.href = '/'

            }
        })
    }

  return (
    <form onSubmit={handleFormSubmit}>
        <label>
            Patient Name:
            <input
            type='text'
            name='PatientName'
            value={patient.PatientName}
            onChange={handleChange}
            required
            />
        </label>
        <label>
            Patient DOB:
            <input
            type='date'
            name='PatientDOB'
            value={patient.PatientDOB}
            onChange={handleChange}
            required
            />
        </label>
        <label>
             DiseaseIdentified:
            <input
            type='text'
            name='DiseaseIdentified'
            value={patient.PatientEmail}
            onChange={handleChange}
            required
            />
        </label>

        <button type='submit'> submit</button>
    </form>
  )
}
export default PatientRegistrationComponent
```
### PatientDeletionComponent
```
import React, { useState } from 'react';
import './PatientDeletionComponent.css'

function PatientDeletionComponent() {
  const [PatientID, setPatientID] = useState('');

  const handleChange = (event) => {
    setPatientID(event.target.value);
  };

  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch(`http://localhost:8080/api/v1/Patient/${PatientID}`,{
      method: 'DELETE'
    })
    .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data deleted successfully`)
                window.location.href = '/'
            }
        })
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Patient ID:
        <input
          type="number"
          name="PatientID"
          value={PatientID}
          onChange={handleChange}
          required
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
};

export default PatientDeletionComponent;
```
## OUTPUT
<img width="960" alt="Screenshot 2023-06-21 230441" src="https://github.com/Shavedha/Hospital-Data-Management-App/assets/93427376/d2964ca5-863c-410a-a545-0aff413b8971">

<img width="959" alt="Screenshot 2023-06-21 225923" src="https://github.com/Shavedha/Hospital-Data-Management-App/assets/93427376/4a741384-d6d4-4d33-95df-726bd88cb2b8">

## RESULT
Thus a Hospital Management data is successfully created using React,SpringBoot and SQL.
