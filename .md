#### Question 1: What is the total revenue generated from all bookings?
```
SELECT 
    SUM(fb.revenue_realized) as Revenue
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date;
```
#### Question 2: How many total bookings were made?
```
SELECT 
    COUNT(fb.booking_id) as Total_Bookings
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date;
```
#### Question 3: What is the total room capacity available?
```
SELECT 
    SUM(fab.capacity) as Total_Capacity
FROM fact_aggregated_bookings fab
INNER JOIN dim_hotels dh ON fab.property_id = dh.property_id
INNER JOIN dim_date dd ON fab.check_in_date = dd.date;
```
####  Question 4: How many successful bookings were made?
```
SELECT 
    SUM(fab.successful_bookings) as Total_Successful_Bookings
FROM fact_aggregated_bookings fab
INNER JOIN dim_hotels dh ON fab.property_id = dh.property_id
INNER JOIN dim_date dd ON fab.check_in_date = dd.date;
```
#### Question 5: What is the occupancy percentage?\
```
SELECT 
    CASE 
        WHEN SUM(fab.capacity) = 0 THEN 0 
        ELSE (SUM(fab.successful_bookings) * 100.0) / SUM(fab.capacity)
    END as Occupancy_Percentage
FROM fact_aggregated_bookings fab
INNER JOIN dim_hotels dh ON fab.property_id = dh.property_id
INNER JOIN dim_date dd ON fab.check_in_date = dd.date;
```
#### Question 6: What is the average rating given by customers?
```
SELECT 
    AVG(CAST(fb.ratings_given as DECIMAL(3,2))) as Average_Rating
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date
WHERE fb.ratings_given IS NOT NULL;
```
#### Question 7: How many days are covered in the date range?
```
SELECT 
    DATEDIFF(day, MIN(dd.date), MAX(dd.date)) + 1 as No_of_Days
FROM dim_date dd;
```
#### Question 8: How many bookings were cancelled?
```
SELECT 
    COUNT(fb.booking_id) as Total_Cancelled_Bookings
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date
WHERE fb.booking_status = 'Cancelled';
```
#### Question 9: What is the cancellation percentage?
```
WITH booking_stats AS (
    SELECT 
        COUNT(fb.booking_id) as total_bookings,
        SUM(CASE WHEN fb.booking_status = 'Cancelled' THEN 1 ELSE 0 END) as cancelled_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN total_bookings = 0 THEN 0 
        ELSE (cancelled_bookings * 100.0) / total_bookings 
    END as Cancellation_Percentage
FROM booking_stats;
```
#### Question 10: How many bookings were checked out successfully?
```
SELECT 
    COUNT(fb.booking_id) as Total_Checked_Out
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date
WHERE fb.booking_status = 'Checked Out';
```
#### Question 11: How many bookings resulted in no-show?
```
SELECT 
    COUNT(fb.booking_id) as Total_No_Show_Bookings
FROM fact_bookings fb
INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
INNER JOIN dim_date dd ON fb.check_in_date = dd.date
WHERE fb.booking_status = 'No Show';
```
#### Question 12: What is the no-show rate percentage?
```
WITH booking_stats AS (
    SELECT 
        COUNT(fb.booking_id) as total_bookings,
        SUM(CASE WHEN fb.booking_status = 'No Show' THEN 1 ELSE 0 END) as no_show_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN total_bookings = 0 THEN 0 
        ELSE (no_show_bookings * 100.0) / total_bookings 
    END as No_Show_Rate_Percentage
FROM booking_stats;
```
#### Question 13: What is the booking percentage by each platform?
```
WITH platform_bookings AS (
    SELECT 
        fb.booking_platform,
        COUNT(fb.booking_id) as platform_bookings,
        (SELECT COUNT(booking_id) FROM fact_bookings) as total_all_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
    GROUP BY fb.booking_platform
)
SELECT 
    booking_platform,
    (platform_bookings * 100.0) / total_all_bookings as Booking_Percentage_by_Platform
FROM platform_bookings;
```
#### Question 14: What is the booking percentage by room class?
```
WITH room_class_bookings AS (
    SELECT 
        dr.room_class,
        COUNT(fb.booking_id) as room_class_bookings,
        (SELECT COUNT(booking_id) FROM fact_bookings) as total_all_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
    LEFT JOIN dim_rooms dr ON fb.room_category = dr.room_class
    GROUP BY dr.room_class
)
SELECT 
    room_class,
    (room_class_bookings * 100.0) / total_all_bookings as Booking_Percentage_by_Room_Class
FROM room_class_bookings;
```
#### Question 15: What is the Average Daily Rate (ADR)?
```
WITH revenue_bookings AS (
    SELECT 
        SUM(fb.revenue_realized) as total_revenue,
        COUNT(fb.booking_id) as total_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN total_bookings = 0 THEN 0 
        ELSE total_revenue / total_bookings 
    END as ADR
FROM revenue_bookings;
```
#### Question 16: What is the realisation percentage?
```
WITH realisation_stats AS (
    SELECT 
        COUNT(fb.booking_id) as total_bookings,
        SUM(CASE WHEN fb.booking_status = 'Cancelled' THEN 1 ELSE 0 END) as cancelled_bookings,
        SUM(CASE WHEN fb.booking_status = 'No Show' THEN 1 ELSE 0 END) as no_show_bookings
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    (1 - ((cancelled_bookings + no_show_bookings) * 1.0 / total_bookings)) * 100 as Realisation_Percentage
FROM realisation_stats;
```
#### Question 17: What is the Revenue Per Available Room (RevPAR)?
```
WITH revpar_stats AS (
    SELECT 
        SUM(fb.revenue_realized) as total_revenue,
        (SELECT SUM(capacity) FROM fact_aggregated_bookings) as total_capacity
    FROM fact_bookings fb
    INNER JOIN dim_hotels dh ON fb.property_id = dh.property_id
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN total_capacity = 0 THEN 0 
        ELSE total_revenue / total_capacity 
    END as RevPAR
FROM revpar_stats;
```
#### Question 18: What is the Daily Booked Room Nights (DBRN)?
```
WITH dbrn_stats AS (
    SELECT 
        COUNT(fb.booking_id) as total_bookings,
        (DATEDIFF(day, MIN(dd.date), MAX(dd.date)) + 1) as no_of_days
    FROM fact_bookings fb
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN no_of_days = 0 THEN 0 
        ELSE total_bookings * 1.0 / no_of_days 
    END as DBRN
FROM dbrn_stats;
```
#### Question 19: What is the Daily Sellable Room Nights (DSRN)?
```
WITH dsrn_stats AS (
    SELECT 
        SUM(fab.capacity) as total_capacity,
        (DATEDIFF(day, MIN(dd.date), MAX(dd.date)) + 1) as no_of_days
    FROM fact_aggregated_bookings fab
    INNER JOIN dim_date dd ON fab.check_in_date = dd.date
)
SELECT 
    CASE 
        WHEN no_of_days = 0 THEN 0 
        ELSE total_capacity * 1.0 / no_of_days 
    END as DSRN
FROM dsrn_stats;
```
#### Question 20: What is the Daily Utilized Room Nights (DURN)?
```
WITH durn_stats AS (
    SELECT 
        COUNT(fb.booking_id) as total_checked_out,
        (DATEDIFF(day, MIN(dd.date), MAX(dd.date)) + 1) as no_of_days
    FROM fact_bookings fb
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
    WHERE fb.booking_status = 'Checked Out'
)
SELECT 
    CASE 
        WHEN no_of_days = 0 THEN 0 
        ELSE total_checked_out * 1.0 / no_of_days 
    END as DURN
FROM durn_stats;
```
#### Question 21: What is the week-over-week change percentage in revenue?
```
WITH weekly_revenue AS (
    SELECT 
        dd.week_no,
        SUM(fb.revenue_realized) as weekly_revenue
    FROM fact_bookings fb
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
    GROUP BY dd.week_no
),
revenue_comparison AS (
    SELECT 
        wr.week_no,
        wr.weekly_revenue as current_week_revenue,
        LAG(wr.weekly_revenue, 1) OVER (ORDER BY wr.week_no) as previous_week_revenue
    FROM weekly_revenue wr
)
SELECT 
    week_no,
    current_week_revenue,
    previous_week_revenue,
    CASE 
        WHEN previous_week_revenue = 0 OR previous_week_revenue IS NULL THEN 0
        ELSE ((current_week_revenue - previous_week_revenue) * 100.0) / previous_week_revenue
    END as Revenue_WoW_Change_Percentage
FROM revenue_comparison
WHERE week_no = (SELECT MAX(week_no) FROM dim_date) ;

```
#### Question 22: What is the week-over-week change percentage in ADR?
```
WITH weekly_adr AS (
    SELECT 
        dd.week_no,
        CASE 
            WHEN COUNT(fb.booking_id) = 0 THEN 0 
            ELSE SUM(fb.revenue_realized) / COUNT(fb.booking_id)
        END as weekly_adr
    FROM fact_bookings fb
    INNER JOIN dim_date dd ON fb.check_in_date = dd.date
    GROUP BY dd.week_no
),
adr_comparison AS (
    SELECT 
        wa.week_no,
        wa.weekly_adr as current_week_adr,
        LAG(wa.weekly_adr, 1) OVER (ORDER BY wa.week_no) as previous_week_adr
    FROM weekly_adr wa
)
SELECT 
    week_no,
    current_week_adr,
    previous_week_adr,
    CASE 
        WHEN previous_week_adr = 0 OR previous_week_adr IS NULL THEN 0
        ELSE ((current_week_adr - previous_week_adr) * 100.0) / previous_week_adr
    END as ADR_WoW_Change_Percentage
FROM adr_comparison
WHERE week_no = (SELECT MAX(week_no) FROM dim_date);


WHERE week_no = (SELECT MAX(week_no) FROM dim_date);
```
