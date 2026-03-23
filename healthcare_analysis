use hospital_management;

-- Query1- total number of registered patients
select count(*) as total_patients
from patients;

-- Query2- Provide the second patient row
select * from
patients
limit 1 offset 1;

-- Query3- Number of patients registered in last 30 days
select count(*) as total_patients
from patients
where registration_date >= (select max(registration_date) - interval 30 day from patients)
order by total_patients desc;
-- Insights - Only one patient is registered from last month. If it continues it could affect the utilisation of the hospital.alter

-- Query4- Total docotrs available in the hospital
Select count(*) total_doctors from doctors;
-- Insight - Total doctors in the hospital is 10.

-- Query5- Distinct specialisation in the hospital
Select distinct specialization 
from 
doctors;

-- Query6- Sort the doctors on the basis of their experience and provide full name
select concat(first_name, ' ', last_name) doctor_name,
years_experience 
from
doctors
order by years_experience  desc;
-- Insights - Most of the doctors have experience more than 20 years.

-- Query7- Name of the doctor ending with 'ne' based on first_name
select first_name 
from 
doctors 
where first_name like '%ne';

-- Query8- Total number of rows in appointments
select count(*) from appointments;
-- Insights- There are overall 200 appointments

-- Query9- The appointment status distribution 
select status, count(*) 
from 
 appointments
 group by status;
 -- Insights - The total of No-show and cancelled is more than the completed appointmnet which is concerning.
 
 -- Query10- Status type whose count is more than 50
 select status, count(status) as total
 from appointments
group by status
having total > 50;

-- Query11- All the appointments of last 7 days
select * 
from 
appointments
where appointment_date >= (select max(appointment_date) - interval 7 day from appointments);
-- Insight - there are only four appointments in last 7 days. 
-- Need to be analyzed the pattern of appointments.

-- Query12- Find datewise count of status
select appointment_date, count(*) as count, status 
from 
appointments
group by appointment_date, status;


-- Query13- select total number of rows in treatments
select count(*) as total 
from 
treatments;

-- Query14- Most common type of treatment
select treatment_type, 
count(treatment_type) total
from 
treatments 
group by treatment_type
order by total desc;
 -- Insights - Most common type of treatment is chemotherapy.
 
 
 -- Query15- Minimum, maximum and average cost for each treatment
  select  
  treatment_type, min(cost) Min_cost
  from 
  treatments
  group by treatment_type;
  
  select  
  treatment_type, max(cost) Max_cost
  from 
  treatments
  group by treatment_type;
  
  select  
  treatment_type, Round(avg(cost) , 2) avg_cost
  from 
  treatments
  group by treatment_type;
  
  -- Query16- total rows in billing
  select count(*) as total from billing;
  
  -- Query17- Payment Status Distribution
  select payment_status, 
  count(payment_status) as total
  from 
  billing
  group by payment_status
  order by total desc;
  -- Insights - Pending amount is more than the paid amount.
  
  -- Query18- How many patients are registered from each address
select address, count(*) as total
from 
patients
group by address
order by total desc;

-- Query19- Find the age of each patient
 select first_name, gender, 
 timestampdiff(year, date_of_birth, curdate()) as age
 from 
 patients;

-- Query20- Age group distribution of patients (IF<18 then under 18, if 18-35 then Adult,if 36-55 then matured else senior)
select patient_id, first_name, gender, timestampdiff(year, date_of_birth, curdate()) as age,
case 
when timestampdiff(year, date_of_birth, curdate()) <18 then 'UNDER 18'
when timestampdiff(year, date_of_birth, curdate()) between 18 and 35 then 'ADULT'
when timestampdiff(year, date_of_birth, curdate()) between 36 and 55 then 'MATURED'
else 'SENIOR'
end as Age_Distribution
from 
patients;

-- Query21- Which email domain is commonly used by the patients
select substring_index(email, '@', -1) as email_domain,
count(*) as total
from 
patients
group by email_domain;

-- Query22- Which month had higher patient registration
select * from patients;
select month(registration_date) as Mon,
count(*) as total
from patients
group by mon
order by total desc;


-- Query23- Which medical specialisation are most in demand based on appointment volume?
select  d.specialization, count(a.appointment_id) as total 
from doctors d join appointments a on d.doctor_id = a. doctor_id
group by  specialization
order by total desc;

-- Query24- Are critical specialization supported by senior doctors or junior doctors?
-- Doctor who has more than 15 years of experience is senior doctor.
select specialization, 
sum(case when years_experience >= 15 then 1 else 0 end) as Senior,
sum( case when years_experience < 15 then 1 else 0 end) as Junior
from doctors
group by specialization;
-- Insights - Only one junior doctor supporting Dermatology rest all the doctors are senior doctors.

-- Query25- Make a table / Masterdata of appointments with patient details and doctor specialization.
select concat(p.first_name, ' ', p.last_name) as patient_name,
p.patient_id, concat(d.first_name, ' ' , d.last_name) as doctor_name,
p.date_of_birth, p.registration_date
from appointments a join doctors d on a.doctor_id = d.doctor_id
join patients p on p.patient_id = a.patient_id;

-- Query26- Which doctors are overloaded and which have available capacity based on the appointment volume?
select concat(d.first_name, ' ', d.last_name) as doctor_name,
count(a.appointment_id) as total 
from doctors d join appointments a on d.doctor_id = a.doctor_id
group by doctor_name
order by total desc;
-- Insights - All the doctors have more than 10 appointments.

-- Query27- Build a big masterdata where we can see the entire journey of a patient. Appointment, treatment and billing.
select concat(p.first_name, ' ', p.last_name) as patient_name,
 a.appointment_id, d.doctor_id, 
concat(d.first_name, ' ', d.last_name) doctor_name,
b.treatment_id, t.treatment_type, b.amount, b.payment_status
from appointments a join patients p on a.patient_id = p.patient_id
join doctors d on d.doctor_id = a.doctor_id
join treatments t on t.appointment_id = a.appointment_id
join billing b on b.patient_id = a.patient_id;

-- Query28- what is the total revenue generaed by the hospital
select Round(sum(amount),2) as total_revenue
from 
billing
where payment_status = 'Paid';

-- Query29- Which patient contribute the most revenue
select patient_id, Round(sum(amount),2) as total_paid
from 
billing
where payment_status = 'Paid'
group by patient_id
order by total_paid desc;
-- Insight - patient_id P012 paid the most of the revenue.alter

-- Query30- Are there any treatments with unusally high cost that require review ( Outlier detection)

select treatment_id,
treatment_type,
cost 
from treatments
where cost > (select avg(cost) + 2*stddev(cost)  from treatments);
-- Insights - There is no outlier.

-- Query31- Rank doctors by total appointment
select concat(d.first_name, ' ' , d.last_name) as doctor_name,
count(a.appointment_id) as total_appointment
from appointments a join doctors d on a.doctor_id = d.doctor_id
group by doctor_name
order by total_appointment desc;

-- Query32- Rank patients by total spending 
select concat(p.first_name, ' ', p.last_name) patient_name,
sum(b.amount) as total_amount,
dense_rank() over (order by sum(b.amount) desc) spending_rank
from patients p join billing b on p.patient_id = b.patient_id
group by patient_name;

-- Query33- Rank treatment by frequency 
select treatment_type,
count(appointment_id) as total,
dense_rank() over (order by count(appointment_id) desc) rnk
from 
treatments
group by treatment_type;
-- Insights - Maximum patients are suffering from cancer.


-- Query34- Are there appointment statuses that indicate patient disengagement risk?
select count(appointment_id) as total, status
from
appointments
group by status; 
-- Insights - Total of no-show and cancelled is more than the scheduled and completed appointment. Which shows the cancellation rates are more than the scheduled.
-- Insights- The management needs to review the pattern completely.


-- Query35- Are there any treatment with usually high cost that requires review?(>1.5STDDEV)
select treatment_id, treatment_type, cost 
from 
treatments 
where cost > (select avg(cost) + 1.5*stddev(cost) from treatments);
-- Insights - there are quite a few treatments which costs more than the average charge management charges for the treatments.

-- Query36- Monthly appointment trend 
select year(appointment_date) as Year_,
month(appointment_date) as Month_,
count(appointment_id) total_appointment
from appointments
group by Year_,Month_
order by total_appointment desc;

-- Query37- Appointments by day of week
select dayname(appointment_date) week_day,
count(appointment_id) as total_appointment
from 
appointments
group by week_day
order by total_appointment desc;

-- Query38- Monthly revenue trend
select year(bill_date) Year, Month(bill_date) Month,
sum(amount) as total
from 
billing 
where payment_status = 'Paid'
group by Year, Month;

-- Query39- appointment sequence per patient
select patient_id, appointment_id, appointment_date,
row_number() over (partition by patient_id order by appointment_date) as visit_number
from 
appointments;

-- Query40- What is gap between patient visits
select patient_id, appointment_id,
datediff(appointment_date, lag(appointment_date) over (partition by patient_id order by appointment_date)) day_between_visits
from
appointments;





