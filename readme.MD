 
# my Retirement calculator in JQuery and Bootstrap

![](images/myRetirmentCalculatorScreen.png?raw=true) I decided to create a modern, easy to use and practical on-line retirement calculator, and I open-sourced it too!  This  retirement calculator comes from the FIRE ( Financially Independent Retire Early) perspective , where you're goal is to save as much as possible , then after a certain number of years, you can retire and live off your savings and investments.  I tried to take into account all the major factors affecting your earning and retirement years, incomes, savings, retirement, kids, housing, healthcare, taxes , inflation etc..

  * Complete detailed description here: http://www.abrandao.com/2019/04/retirement-calculator/
  
## Demo
  * Link to Live calculator :  http://www.abrandao.com/retire


## Getting Started

Clone this repo .. or just download the source code to the **index.html** file. All the calculation logic is located there.. 

  * this repository, mainly the **index.html** file 
  * knowledge of HTML5, very basic understanding Jquery and some understanding of Bootstrap CSS/HTML

## Dependencies 

This is a single web-page calculator with minimal dependencies. All the calculation code is contained in the **index.html** file.
All the depedencies are supplied via links to CDN . 

The following links are required
  * [BootStrap](https://getbootstrap.com/) · The most popular HTML, CSS, and JS library in the world . 
  * [Jquery](https://jquery.com/). jQuery is a fast, small, and feature-rich JavaScript library. 
  * [Chart.js](https://www.chartjs.org/) Simple yet flexible JavaScript charting for designers & developers

Inside the <head> tag
```javascript
 <!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
```

Then the corresponding JQuery and ChartJS links be sure to reference Jquery library 

```javascript

 <!-- jQuery library -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
 <!-- then Bootstrap JS -->
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
<!-- Charting ChartJS.com from CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@2.8.0"></script>
```

You can of course download the dependencie files and modify the above src links to point all to your local folders.

### Installing

Just clone this repository and open *index.html* in your web browser. For off line use , modify the src for dependencies listed above.

## Calculation Model Approach 


Essentially all your Income and SAVINGS are added up during the years, your working (working income) , and savings+retirement accounts  + other income over the course of your lifetime. Then these various forms of income are calculated against entered  Annual Percentage Yield to give your annual compound interest growth.  Here’s a sample of the calculation code for this section:
```javascript
for (i = 1 ; i < years+1; i++) { 
  var ai = {} ;  //define a Income blank object
    var exp={} ; //define an Expense object
   //create an Annual Income object
  ai.i=(i);
  ai.age=(+i + +currentAge);
  ai.income_earned= ( i <= work_years  ) ? parseInt( $("#income").val()  ) : 0 ;
   ai.income_saved = ( i <= work_years  ) ? $("#income").val()  * parseFloat( $("#income_savings_rate").val()/100 ) : 0 ; 
  
  ai.savings= parseFloat( $("#savings").val() ) ; 
  ai.savings_Int= +$("#savings").val()* parseFloat ( $("#savings_apy").val()/100 );
  ai.retirement=parseFloat(  $("#retirement").val() )  ; 
  ai.retirement_Int=  parseInt( $("#retirement").val() ) * parseFloat( $("#retirement_apy").val()/100 ) ;
  ai.one_time=(ai.age==inheritance_age) ? parseInt( $("#income_inheritance").val() ) : 0 ;
  ai.rents= parseFloat( $("#income_realestate_rentals").val() ) ;
  ai.pension=(ai.age>= ssn_year_eligible ) ? parseFloat(  $("#income_pension").val() ) : 0 ;
    ai.misc= parseFloat( $("#income_misc").val() ) ;
  ai.ssn= (  ai.age >= ssn_year_eligible ) ? parseFloat( $("#income_ss").val() ) : 0 ;
```

  On the other side we have your EXPENSES . Expenses are more linear over your lifetime, income taxes are calculated based on 2018-2019 tax bracket, and applied to against your annual income.  Housing expenses have a term if you’re a home owner, with a separate field for perpetual costs like property taxes,  insurance, maintenance etc.  The number of dependents count as an expense, based on current pricing models of how much it costs to raise a child to 18. The other expense are pretty self-explanatory, adjust the default values  to better suit your particular circumstances.

Once we have all that information we  calculate your INCOME – EXPENSES we get a yearly surplus (or deficit), which we will apply to your SAVINGS, hopefully and this is the premise of this calculator.  You can work just long enough to grow your SAVINGS to the point where you can retire and only need to take a small percentage and live off that.  Sample Code:

```javascript
// Now calculate SURPLUS OR DEFICIT for the year
  var diff=(incomes[i-1].total_annual_income.toFixed(2) - expenses[i-1].total_annual_expenses.toFixed(2) );
  // any money left over place in savings for that year.
          if ( i > 1 )
          incomes[i-1].savings =( incomes[i-1].savings + diff );
```

Finally we calculate what you’re withdrawal  rate is and how it will affect your savings over time after your retire. . The conventional wisdom of 4% withdrawal works out pretty well and is the default withdrawal rate. Adjusting this withdrawal rate. and the number of years you are working will have some of the largest impact as to what you can take out in retirement. Because you’ll have a larger nest egg at the beginning , your withdrawal percentage will likely yield a larger income than in your later years , which is why I display two retirement income values. Here’s a sample:
```javascript
//Now calculate how much to withdraw from savings and retirement accounts AFTER we're RETIRED
 if (i > work_years) //now we're withdrawing
     {
   
     ai.retirement_income=(withdraw_rate*ai.savings) +  (withdraw_rate*ai.retirement  );  
       
       ai.savings = ai.savings-(withdraw_rate*ai.savings); //subtract % from savings 
       //TODO: adjust retirement withdraw only after AGE 60 (USA time when no penalty)
         ai.retirement= ai.retirement - (withdraw_rate*ai.retirement  ) ; //subract % from retirment investments
     
     //console.log(i+" > workyears+2 "+(+work_years+2)+" withdraw:"+withdraw_rate+ " on savings "+ai.savings);
      if ( (+work_years+1)==i )  //Show first and last year how much retirement income we get.
       $("#retirement_income_start").text(formatUSD(ai.retirement_income));
     else
     $("#retirement_income_end").text(formatUSD(ai.retirement_income));  
     $("#retirement_savings_end").text(formatUSD(ai.savings));  
   
     }
``` 

There are a few one-time events, such as inheritance or sale (of a primary residence or business) which you choose how much and at what time point it occurs. Also Social Security you decide when to take it. If you have a pension , annual pension disbursements begin when social security begins.  Code for inheritance looks like this.

```javascript
ai.one_time=(ai.age==inheritance_age) ? parseInt( $("#income_inheritance").val() ) : 0 ;
```
## Verifying values and calculation code

Right now I simply send to the console.log a year-by-year summary of the income and expense javascript objects, you can use this to debug in your browser by pressing **F12** developer mode. and see something like.

'''
 {"i":30,"tax_bracket":0.37,"income_tax":-1741.82,"healthcare":4358,"housing":0,"property_tax":2000,"dependants":0,"food":4500,"utilities":2060,"transport":9500,"optional":0,"inflation_rate":0.015,"total_annual_expenses":20986.3227,"inflation_costs":314.7948405}
 Annual Income: -4707.63 -  20986.32 Expenses  =   -25693.95
{"i":31,"tax_bracket":0.37,"income_tax":-1892.8,"healthcare":4358,"housing":0,"property_tax":2000,"dependants":0,"food":4500,"utilities":2060,"transport":9500,"optional":0,"inflation_rate":0.015,"total_annual_expenses":20833.078,"inflation_costs":312.49617}
 Annual Income: -5115.69 -  20833.08 Expenses  =   -25948.77
 '''

## Installing and Testing 

  *   Download and open **index.html** in your web browser.

## Contributing

If you're interested in updating this script or have some ideas feel free to get in touch. Use Github to report issues and suggest enhancements.

## Versioning

  * April 3, 2019 - Initial release  version


## Authors
 
  * abrandao29@gmail.com

## License

This project is licensed under the BSD License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to all the folks that gave us Bootsrap library and Jquery  and ChartJS for makinng it infinitately easier
* Inspiration: 2Cents Youtube video here: https://youtu.be/8si7cqw9wm0 and the general FIRE movement.. 
* etc
