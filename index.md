# ATAC-Monitor

The dataset is based on the [Open Data](https://romamobilita.it/it/tecnologie/open-data) by `romamobilita.it`. I collect and store the expected waiting time for bus lines in Rome. For __every bus__ line, we consider the second-last station in the trip. We measure the minutes to be waited for the next upcoming bus at that station. I collect this measure for every bus line every __3 minutes__. This data is stored, and this page shows some statistics.

## Average waiting minutes

We can see the distribution of waiting minutes for every bus line. Every bar in the chart represents a bus line. The length of the bar shows the average amount of minutes to wait.

<div id="wrapper" style="height:480px; width:640px;">
  <canvas id="chart"></canvas>
</div>


Note that this average may under-estimate the real waiting time. If you are at a stop, but no bus is upcoming, there is no _expected waiting time_. This statistics only takes in account waiting time when a bus left the terminus and is actually driving to the station.

## Average waiting time last week

We compute the average waiting time on all the lines for every day of the last week.

<iframe style="border: none;border-radius: 2px;box-shadow: 0 2px 10px 0 rgba(70, 76, 79, .2);" width="640" height="480" src="https://charts.mongodb.com/charts-project-0-urdrh/embed/charts?id=aa70bd0d-f3b7-4ee3-870c-e7c015e20a09&tenant=b6ce3d2e-8588-4414-bc01-ab06e40b3635"></iframe>

## Average waiting over the day

For every hour of the day, we compute the maximum and the average waiting time for all the __bus lines__.

<iframe style="border: none;border-radius: 2px;box-shadow: 0 2px 10px 0 rgba(70, 76, 79, .2);" width="640" height="480" src="https://charts.mongodb.com/charts-project-0-urdrh/embed/charts?id=511d7883-016b-4510-9a66-2902f0af5fd8&tenant=b6ce3d2e-8588-4414-bc01-ab06e40b3635"></iframe>

On the X-axis we have the hour of the day (`13 = 13:00-13:59`). The lines represents the average waiting time during that hour for all the bus lines.

## Longest waiting time

<iframe style="border: none;border-radius: 2px;box-shadow: 0 2px 10px 0 rgba(70, 76, 79, .2);" width="640" height="480" src="https://charts.mongodb.com/charts-project-0-urdrh/embed/charts?id=dba11411-a162-47b6-be9a-d141602f372b&tenant=b6ce3d2e-8588-4414-bc01-ab06e40b3635"></iframe>

This is the longest waiting time in minutes shown at a bus station.

## Info

You can contact me for info about the dataset or if you want to contribute to the reporting statistics.

- [GitHub](https://github.com/Marco-Santoni/atacmonitor)
- [LinkedIn](https://linkedin.com/in/msantoni)
- [Twitter](https://twitter.com/mrsantoni)


   <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.8.0/Chart.min.js"></script>
   <script src="https://d3js.org/d3.v5.min.js"></script>
   <script type="text/javascript">
     function makeChart(lines) {
       var routes = lines.map(function(d) {
         return d.route_name;
       });
       var waitingTime = lines.map(function(d) {
         return (d.waiting_time / 60);
       });

       arrayOfObj = routes.map(function(d, i) {
         return {
           label: d,
           data: waitingTime[i] || 0
         };
       });

       sortedArrayOfObj = arrayOfObj.sort(function(a, b) {
         return b.data > a.data;
       });

       sortedRoutes = [];
       sortedWaitingTime = [];
       sortedArrayOfObj.forEach(function(d) {
         sortedRoutes.push(d.label);
         sortedWaitingTime.push(d.data);
       });


       var chart = new Chart('chart', {
         type: "horizontalBar",
         options: {
           maintainAspectRatio: false,
           legend: {
             display: false
           },
           scales: {
             yAxes: [{
               ticks: {
                 autoSkip: true,
                 maxTicksLimit: 40,
               }
             }],
           }
         },
         data: {
           labels: sortedRoutes,
           datasets: [{
             data: sortedWaitingTime,
             borderColor: '#5bcdb4',
             backgroundColor: '#5bcdb4',
             borderWidth: 5
           }]
         },
       });
     }

     // Request data using D3
     d3
       .csv("https://stops-feed-results.s3.amazonaws.com/average_waiting_time_minutes.csv")
       .then(makeChart);
   </script>
 </body>
