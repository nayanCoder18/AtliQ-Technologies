### To get the total revenue_realized
```
Revenue = SUM(fact_bookings[revenue_realized])
```
### To get the total number of bookings happened
```
Total Bookings = COUNT(fact_bookings[booking_id])
```
### To get the total capacity of rooms present in hotels
```
Total Capacity = SUM(fact_aggregated_bookings[capacity])
```
### To get the total succesful bookings happened for all hotels
```
Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])
```
### Occupancy means total successful bookings happened to the total rooms available(capacity)
```
Occupancy % = DIVIDE([Total Succesful Bookings],[Total Capacity],0)
```
### Get the average ratings given by the customers
```
Average Rating = AVERAGE(fact_bookings[ratings_given])
```
### To get the total number of days present in the data.
### In our case, we have data from May to July. So 92 days.
```
No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1
```
### To get the"Cancelled" bookings out of all Total bookings happened
```
Total cancelled bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")
```
### calculating the cancellaton percentage.
```
Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])
```
### To get the successful 'Checked out' bookings out of all Total bookings happened
```
Total Checked Out = CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")
```
### To get the"No Show" bookings out of all Total bookings happened 
 - ("No show" means those customers who neither cancelled nor attend to their booked rooms)
```
Total no show bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")
```
### calculating the no show percentage.
```
No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])
```
### To show the percentage contribution of each booking platform for bookings in hotels.
 - We have booking platforms like makeyourtrip, logtrip, tripster etc
```
Booking % by Platform = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(fact_bookings[booking_platform])
  ))*100"
```
### To show the percentage contribution of each room class over total rooms booked.
 - We have room classes like Standard, Elite, Premium, Presidential.
```
Booking % by Room class = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(dim_rooms[room_class])
  ))*100"
```
### Calculate the ADR(Average Daily rate)
 - It is the ratio of revenue to the total rooms booked/sold. 
 - It is the measure of the average paid for rooms sold in a given time period
```
ADR = DIVIDE( [Revenue], [Total Bookings],0)
```
### calculate  the realisation percentage.
 -It is nothing but the succesful "checked out" percentage over all bookings happened.
```
Realisation % = 1- ([Cancellation %]+[No Show rate %])
```
###Calculate the RevPAR(Revenue Per Available Room)
 - RevPAR represents the revenue generated per available room, whether or not they are occupied. RevPAR helps hotels measure their revenue generating performance to accurately price rooms. RevPAR can help hotels measure themselves against other properties or brands.
```
RevPAR = DIVIDE([Revenue],[Total Capacity])
```
### calculate DBRN(Daily Booked Room Nights)
 - This metrics tells on average how many rooms are booked for a day considering a time period
```
DBRN = DIVIDE([Total Bookings], [No of days])
```
### calculate DSRN(Daily Sellable Room Nights)
 - This metrics tells on average how many rooms are ready to sell for a day considering a time period
```
DSRN = DIVIDE([Total Capacity], [No of days])
```
### calculate DURN(Daily Utilized Room Nights)
 - This metric tells on average how many rooms are succesfully utilized by customers for a day considering a time period
```
DURN = DIVIDE([Total Checked Out],[No of days])
```
### To get the revenue change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
Revenue WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Revenue],dim_date[wn]= selv)
var revpw =  CALCULATE([Revenue],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return

DIVIDE(revcw,revpw,0)-1
```
### To get the occupancy change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
Occupancy WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Occupancy %],dim_date[wn]= selv)
var revpw =  CALCULATE([Occupancy %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return

DIVIDE(revcw,revpw,0)-1
```
### To get the ADR(Average Daily rate) change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
ADR WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([ADR],dim_date[wn]= selv)
var revpw =  CALCULATE([ADR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1
```
### To get the RevPar(Revenue Per Available Room) change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
Revpar WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([RevPAR],dim_date[wn]= selv)
var revpw =  CALCULATE([RevPAR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return

DIVIDE(revcw,revpw,0)-1"
```
### To get the Realisation change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
Realisation WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Realisation %],dim_date[wn]= selv)
var revpw =  CALCULATE([Realisation %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return

DIVIDE(revcw,revpw,0)-1"
```
### To get the DSRN(Daily Sellable Room Nights) change percentage week over week.
 - Here, 
 - revcw  for current week
 - revpw for previous week
```
"DSRN WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([DSRN],dim_date[wn]= selv)
var revpw =  CALCULATE([DSRN],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return

DIVIDE(revcw,revpw,0)-1"
```
