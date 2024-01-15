
# SQL Murder Mystery

A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. 
You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. 
Start by retrieving the corresponding crime scene report from the police department’s database.

![enter image description here](https://mystery.knightlab.com/schema.png)

1st we will check crime_scene_report to identify which murder we should check.

    SELECT
     *
    FROM
    Crime_scene_report
    WHERE
    date = '20180115' AND type = 'murder'AND City = 'SQL City'

*Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".*

    SELECT
     *
    FROM
    	person
    WHERE 
      	address_street_name = 'Northwestern Dr'
    ORDER BY              
    	address_number DESC
	
id	name	license_id	address_number	address_street_name	ssn
**14887**	Morty Schapiro	118009	4919	Northwestern Dr	111564949

We find out who is 1st witness. Last house of Northwestern Dr is Morty Schapiro ssn **111564949** license_id 118009

    SELECT
     *
    FROM
    	drivers_license
    WHERE 
      	id =118009

id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
**118009**	64	84	blue	white	male	00NU00	Mercedes-Benz	E-Class

    SELECT
     *
    FROM
    	income
    WHERE
    ssn = 111564949

No data returned, there is no information's about his income 

    SELECT
     *
    FROM
    	interview
    WHERE
    	person_id = 148879	

His intreview :

*I heard a gunshot and then saw a **man run** out. He had a "**Get Fit Now Gym**" bag. The membership number on the bag **started with** "**48Z**". Only gold members have those bags. The man got into a car with a plate that **included** "**H42W**".*

    SELECT
     *
    FROM
    	person
    WHERE 
      	address_street_name = 'Franklin Ave' AND name LIKE'Annabel%';
	
	
	

id name license_id address_number address_street_name ssn 
**16371** Annabel Miller 490173 103 Franklin Ave **318771143**


Checked info from Anabell driver license 

    SELECT
     *
    FROM
    	drivers_license
    WHERE
    	id = '490173'
	
	
id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
**490173**	35	65	green	brown	female	23AM98	Toyota	Yaris
	

SELECT
 *
FROM
	income
WHERE
ssn = 318771143

No data about her income 
Now checking intreview with her 

    SELECT
     *
    FROM
    	interview 
    WHERE	
    person_id = 16371  

*I saw the murder happen, and I recognized the killer from my gym when I was working out last week on **January the 9th.***

Checking all members with id starts with 48Z 

    SELECT
     *
    FROM
    get_fit_now_member AS g
    
    WHERE
    id LIKE '48Z%'

id	person_id	name	membership_start_date	membership_status
48Z38	49550	Tomas Baisley	20170203	silver
48Z7A	28819	Joe Germuska	20160305	gold
48Z55	67318	Jeremy Bowers	20160101	gold
one of them has no gold membership, we can pre-asumme that its not him. We will check later if needed  

Now we are checking who was in gym at Jan 9th 2018 with membership ID starts with 48Z

    SELECT
     *
    FROM
    get_fit_now_check_in AS m
    WHERE
    check_in_date = 20180109 AND membership_id LIKE '48Z%'
   We have 1 suspects 
48Z7A AND  48Z55

    SELECT
     *
    FROM
    get_fit_now_check_in AS m
    JOIN
    get_fit_now_member AS g ON
    m.membership_id = g.id
    WHERE
    check_in_date = 20180109 AND membership_id LIKE '48Z%' AND membership_status = 'gold'
   
Checking if both are gold membership
They are.

Now time to check plates number

    SELECT
         *
    FROM
    	drivers_license
    WHERE
    plate_number LIKE '%H42W%'


id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
183779	21	65	blue	blonde	female	H42W0X	Toyota	Prius
423327	30	70	brown	brown	male	0H42W2	Chevrolet	Spark LS
664760	21	71	black	black	male	4H42WR	Nissan	Altima

2 male has plates like that. Lets check who is owner of this cars 

    SELECT
        *
    FROM
    	person 
    
    WHERE
    	license_id = '423327' OR	license_id= '664760'
    
We have our suspect **67318	Jeremy Bowers**
Now interview with him 
  
*I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017.*
With all informastions we found 3 suspects 

    SELECT
    	*
    FROM
    	drivers_license
    WHERE
    	hair_color = 'red'
    AND
    	gender = 'female'
    AND
    	car_make = 'Tesla'
    AND 
    	car_model = 'Model S'


id	age	height	eye_color	hair_color	gender	plate_number	car_make	car_model
202298	68	66	green	red	female	500123	Tesla	Model S
291182	65	66	blue	red	female	08CM64	Tesla	Model S
918773	48	65	black	red	female	917UU3	Tesla	Model S

Now checked who was on events from this 3 ids

    SELECT
    	*
    FROM
    	facebook_event_checkin as f
    JOIN 
     	person as p ON f.person_id = p.id
    	
    WHERE license_id = 202298 OR license_id =291182 OR license_id = 918773
**Miranda Priestly**
