# la12stu
Dimplex 

# Why, yes why?
Oh, Dimplex.  Please refrain from building heat pump control systems.
This is what happened today (Jan 2024):
![grafik](https://github.com/l33tn00b/la12stu/assets/28904067/8f73efcd-cb47-49fb-8e16-c28017873a2a)  

Came home to a cold house (well, much cooler than desired). At approx. 5am the heat pump controller had suddenly decided to up the temperature to more than 40 degrees celsius (bottom chart, blue line). Why? No idea.  Approx. one hour later (at 6 am) it realized the futility of that idea (?) (impossible to reach these temperatures at around zero degrees celsius exteror temperature). Instead of just doing the best it could (running all the time) it threw the towel (red line, temperature slowly decreasing). Shortly after 1pm it got back on its feet, running all afternoon to get back to a normal temperature (red line, slowly increasing temperature). F*ck Dimplex. I hadn't changed any settings. Instead of charging people for "features", could you please just deliver a working product? All that at a price point of more than 1300€. F*ck Dimplex.

Just happy to have all the tracking installed to be able to prove and look into it (wasn't the first time). How on earth is a normal customer supposed handle this? Not even the authorized installer would be able to tell me why it suddenly was so cold..

By the way, this is the corresponding exterior temperature: 
![grafik](https://github.com/l33tn00b/la12stu/assets/28904067/219f2e02-98f6-4d92-89dd-50b11398cc20)


# COP Diagram
![cop_la12stu](https://github.com/l33tn00b/la12stu/assets/28904067/785d30d1-600f-4565-9e10-ab4ed212f557)
(taken from official Dimplex Doc at dimplex-partner.de, see https://www.dimplex-partner.de/pdf/de/produktattribute/produkt_1727909_extern_egd.pdf)  
Why can't they just provide a decent table? No, we'll have to approixmate the values by trying to read the diagram.
```
temps = [-22:2:35]
cop55 = [1, 1.125, 1.3, 1.49, 1.625, 1.75, 2, 2.125, 2.25, 2.375, 2.5, 2.625, 2.75, 2.875, 2.9, 3.125, 3.4 ,3.6, 3.7, 3.8, 3.81, 3.9, 4, 4.125, 4.25, 4.5, 4.8, 5.075, 5.4]
cop45 = [1.4, 1.6, 1.7, 1.875, 2.1, 2.25, 2.375, 2.625, 2.75, 2.825, 3, 3.125, 3.25, 3.49, 3.7, 3.9, 4.2, 4.375, 4.45, 4.625, 4.75, 4.875, 5, 5.125, 5.3, 5.5, 5.75, 6, 6.25]
cop35 = [1.8, 2, 2.2, 2.375, 2.5, 2.7, 2.85, 3, 3.25, 3.375, 3.63, 3.8, 4, 4.25, 4.5, 4.75, 5, 5.5, 5.75, 6, 6.2, 6.375, 6.55, 6.75, 6.9, 7.1, 7.25, 7.3, 7.45]
````

Let's try polynomial fitting; 4th, 7th, 11th, 15th order:
```
p35_4 = polyfit(temps, cop35, 4)
y35_4 = polyval(p35_4, temps)
p35_7 = polyfit(temps, cop35, 7)
y35_7 = polyval(p35_7, temps)
p35_11 = polyfit(temps, cop35, 11)
y35_11 = polyval(p35_11, temps)
p35_15 = polyfit(temps, cop35, 15)
y35_15 = polyval(p35_15, temps)
%%% do subplots to have it all in one figure
%%% we plot all curves in one row 
%%% because y deviation matters and we may see it more clearly like that
figure;subplot(1,4,1),plot(temps, cop35,'x'), hold on, plot(temps, y35_4,'o'), hold off, title({'4th order polynomial fit','for COP at 35°C flow temperature'}), legend('COP Curve','4th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,2),plot(temps, cop35,'x'), hold on, plot(temps, y35_7,'o'), hold off, title({'7th order polynomial fit', 'for COP at 35°C flow temperature'}), legend('COP Curve','7th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,3),plot(temps, cop35,'x'), hold on, plot(temps, y35_11,'o'), hold off, title({'11th order polynomial fit','for COP at 35°C flow temperature'}), legend('COP Curve','11th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,4),plot(temps, cop35,'x'), hold on, plot(temps, y35_15,'o'), hold off, title({'15th order polynomial fit', 'for COP at 35°C flow temperature'}), legend('COP Curve','15th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')

%%% 45 degrees flow temperature
p45_4 = polyfit(temps, cop45, 4)
y45_4 = polyval(p45_4, temps)
p45_7 = polyfit(temps, cop45, 7)
y45_7 = polyval(p45_7, temps)
%%% mimimi, complains about being badly conditioned at 11th order
p45_11 = polyfit(temps, cop45, 11)
y45_11 = polyval(p45_11, temps)
%%% 15th ist fine, though
p45_15 = polyfit(temps, cop45, 15)
y45_15 = polyval(p45_15, temps)
%%% do subplots to have it all in one figure
%%% we plot all curves in one row 
%%% because y deviation matters and we may see it more clearly like that
figure;subplot(1,4,1),plot(temps, cop45,'x'), hold on, plot(temps, y45_4,'o'), hold off, title({'4th order polynomial fit','for COP at 45°C flow temperature'}), legend('COP Curve','4th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,2),plot(temps, cop45,'x'), hold on, plot(temps, y45_7,'o'), hold off, title({'7th order polynomial fit', 'for COP at 45°C flow temperature'}), legend('COP Curve','7th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,3),plot(temps, cop45,'x'), hold on, plot(temps, y45_11,'o'), hold off, title({'11th order polynomial fit','for COP at 45°C flow temperature'}), legend('COP Curve','11th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,4),plot(temps, cop45,'x'), hold on, plot(temps, y45_15,'o'), hold off, title({'15th order polynomial fit', 'for COP at 45°C flow temperature'}), legend('COP Curve','15th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')

%%% 55 degrees flow temperature
p55_4 = polyfit(temps, cop55, 4)
y55_4 = polyval(p55_4, temps)
p55_7 = polyfit(temps, cop55, 7)
y55_7 = polyval(p55_7, temps)
%%% again, 11th order seems to be odd
p55_11 = polyfit(temps, cop55, 11)
y55_11 = polyval(p55_11, temps)
p55_15 = polyfit(temps, cop55, 15)
y55_15 = polyval(p55_15, temps)
%%% do subplots to have it all in one figure
%%% we plot all curves in one row 
%%% because y deviation matters and we may see it more clearly like that
figure;subplot(1,4,1),plot(temps, cop55,'x'), hold on, plot(temps, y55_4,'o'), hold off, title({'4th order polynomial fit','for COP at 55°C flow temperature'}), legend('COP Curve','4th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,2),plot(temps, cop55,'x'), hold on, plot(temps, y55_7,'o'), hold off, title({'7th order polynomial fit', 'for COP at 55°C flow temperature'}), legend('COP Curve','7th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,3),plot(temps, cop55,'x'), hold on, plot(temps, y55_11,'o'), hold off, title({'11th order polynomial fit','for COP at 55°C flow temperature'}), legend('COP Curve','11th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
subplot(1,4,4),plot(temps, cop55,'x'), hold on, plot(temps, y55_15,'o'), hold off, title({'15th order polynomial fit', 'for COP at 55°C flow temperature'}), legend('COP Curve','15th order poly fit','Location','SouthEast'), xlabel('Outside Air Temperature'), ylabel('COP')
```
Let's see our interpolations:
![cop_fitting_35](https://github.com/l33tn00b/la12stu/assets/28904067/a78bdbdc-f1bf-4ed2-879b-d80bbc11bea1)
![cop_fitting_45](https://github.com/l33tn00b/la12stu/assets/28904067/47404212-87fe-41d0-b05f-26d4b91b103f)
![cop_fitting_55](https://github.com/l33tn00b/la12stu/assets/28904067/63ca49fb-130a-48ad-8692-02aace6f94a3)

%%% looking at the interpolation curves makes me think "let's take 15th  
%%% order interpolation".  
%%% mainly because of the behaviour of the 55°C flow temperature curves..      
%%% but then again, how relevant is 55°C flow temperature? The only time we  
%%% reach that level of temperature is when water heating kicks in.  
%%% checked against lowest recorded temperature last year (2022) (~-15°C):   
%%% gave around 40°C flow temperature.   
%%% and this is the point where heating capacity will become  
%%% insufficient and additional purely electrical heating (i.e. resistive)  
%%% will come online. So higher flow temps won't even help in that case.  
%%% we'll take 15th order anyway (not much of an additional effort in terms  
%%% of calculations) because it also gives a nicer fit than 11th order for  
%%% 45 degrees (and we're somewhere in between 45 and 35 degrees worst  
%%% case).  

%%% thinking again... why have a polynomial interpolation at all?   
%%% we might as well just do a linear interpolation for anything in betwwen  
%%% our data points :P   
%%% we'd thus nail the data points and not have to put in additional  
%%% effort. over-engineering...  
