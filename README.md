# Prototype_Shoping_Website
#A prototype website our group had to create
#ControlList.java

1 
  2 import java.util.ArrayList;
  3 import java.util.List;
  4 
  5 /*
  6  * To change this license header, choose License Headers in Project Properties.
  7  * To change this template file, choose Tools | Templates
  8  * and open the template in the editor.
  9  */
 10 
 11 /**
 12  *
 13  * @author Master-Prime
 14  */
 15 public class ControlList {
 16     static Customer gMan;
 17     
 18     
 19     public static int checkLogin(String name, String password)
 20     {
 21         int opt = LibraryDataCD.loginCheck(name, password);
 22         return opt;
 23     }
 24     
 25     public static List<Deal> getDeals()
 26     {
 27         ArrayList<Deal> result = new ArrayList<Deal>(LibraryDataCD.getDealsList());
 28         return result;
 29     }
 30     
 31     public static String[] getCurrDeals()
 32     {
 33         System.out.println("I got through");
 34         String[] res;
 35         res = LibraryDataCD.getCustomerDeals(gMan.getNumber());
 36         return res;
 37         
 38     }
 39 
 40     public static void makegMan(String name)
 41     {
 42        gMan = new Customer(LibraryDataCD.getCustomer(name)); 
 43     }
 44     
 45     public static void subscribeDeal(int dealID, int pMethod, float cost, int mpCost)
 46     {
 47         LibraryDataCD.subDeal(gMan.getNumber(), dealID);
 48         if (pMethod == 1)
 49         {
 50             LibraryDataCD.createCCPaymentLog(gMan.getNumber(), gMan.getCNumber(), gMan.getCHolder(), dealID, pMethod, cost);
 51         }
 52         else
 53         {
 54             LibraryDataCD.createMPPaymentLog(gMan.getNumber(), mpCost, dealID, pMethod);
 55         }
 56     }
 57 
 58     public static void createQuery(String query)
 59     {
 60         LibraryDataCD.queryCreation(gMan.getNumber(), query);
 61     }
 62     
 63     public static int checkSubscription(int dealID)
 64     {
 65         int result;
 66         dealID = dealID + 1;
 67         result = LibraryDataCD.checkSub(gMan.getNumber(), dealID);
 68         return result;
 69     }
 70     
 71     public static List<Order> getOrders()
 72     {
 73         ArrayList<Order> result = new ArrayList<Order>(LibraryDataCD.getCustomerOrders(gMan.getNumber()));
 74         return result;
 75     }
 76     
 77     public static List<Services> getServices()
 78     {
 79         ArrayList<Services> result = new ArrayList<Services>(LibraryDataCD.getServicesList());
 80         return result;
 81     }
 82     
 83     public static List<Products> getProducts()
 84     {
 85         ArrayList<Products> result = new ArrayList<Products>(LibraryDataCD.getProductsList());
 86         return result;
 87     }
 88     
 89     public static List<Packages> getPackages()
 90     {
 91         ArrayList<Packages> result = new ArrayList<Packages>(LibraryDataCD.getPackagesList());
 92         return result;
 93     }
 94     
 95     public static void createDeal(String name, int sID, int pID, float cost, int mpCost)
 96     {
 97         LibraryDataCD.createDeal(name, sID, pID, cost, mpCost);
 98     }
 99 }
 
#LibraryDataCD.java
 
 1 // Skeleton version of public class LibraryData.java that links to a database.
  2 
  3 import java.sql.*; // DB handling package
  4 import java.util.ArrayList;
  5 import java.util.Calendar;
  6 import java.util.List;
  7 import javax.swing.JOptionPane;
  8 import java.util.Date;
  9 
 10 public class LibraryDataCD {
 11 
 12     // Declare the JDBC objects.
 13     private static Connection con = null;
 14     private static Statement stmt = null;
 15 
 16     static {
 17         // standard code to open a connection and statement to SQL Server database
 18         try {
 19             // Create a variable for the connection string.
 20                     String connectionUrl = "jdbc:sqlserver://SQL-SERVER;"
 21                     + "databaseName=" + System.getProperty("user.name") + ";"
 22                     + "integratedSecurity=true;";
 23 
 24             // Establish the connection.
 25             Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
 26             con = DriverManager.getConnection(connectionUrl);
 27 
 28         } // Handle any errors that may have occurred.
 29         catch (SQLException sqle) {
 30             System.out.println("Sql Exception :" + sqle.getMessage());
 31         } catch (ClassNotFoundException e) {
 32             System.out.println("Class Not Found Exception :" + e.getMessage());
 33         }
 34     }
 35     
 36     public static int loginCheck(String user, String pass)
 37     {
 38         try{
 39         stmt = con.createStatement();
 40         String output1 = "";
 41         String output2 = "";
 42         int ret = 1;
 43         
 44         ResultSet res = stmt.executeQuery("SELECT Username, Passwrd FROM Customer Where username = '" + user+ "'");
 45         while(res.next())
 46         {
 47             output1 = res.getString(1);
 48             output2 = res.getString(2);
 49         }
 50         if (output1.isEmpty())
 51         {
 52           ret = 0;  
 53         }
 54         else if (!output2.equals(pass))
 55         {
 56             ret = 0;
 57         } 
 58          return ret;
 59         }
 60          catch (Exception e) {
 61             System.out.println(e);
 62             return -1;
 63          }
 64     }
 65     
 66     public static String[] getCustomer(String name)
 67     {
 68         String[] result = new String[7];
 69         String output = "";
 70         int number = 0;
 71         try{
 72         stmt = con.createStatement();
 73         ResultSet res = stmt.executeQuery("SELECT username, customerid, email, ccname, ccnumber FROM Customer WHERE Username = '" + name + "'");
 74         while(res.next())
 75         {
 76             result[0] = res.getString(1);
 77             result[1] = res.getString(2);
 78             result[2] = res.getString(3);
 79             result[3] = res.getString(4);
 80             result[4] = res.getString(5);
 81                    
 82         }
 83         }
 84             catch (Exception e) {
 85             System.out.println(e);
 86             System.out.println("acc error");
 87         }
 88         return result;
 89     }
 90     
 91     public static int getAccNum(String name)
 92     {
 93         String output = "";
 94         int number = 0;
 95         try{
 96         stmt = con.createStatement();
 97         ResultSet res = stmt.executeQuery("SELECT CustomerID FROM Customer WHERE Username = '" + name + "'");
 98         if(res.next())
 99         {
100             return number = Integer.parseInt(res.getString(1));
101         }
102         else
103         {
104             return 0;
105         }
106         }
107          catch (Exception e) {
108             System.out.println(e);
109             System.out.println("acc error");
110             return -1;
111         }
112     }
113     
114     public static String getEmail(String name)
115     {
116         try{
117         stmt = con.createStatement();
118         String output = "";
119         String email = "";
120         ResultSet res = stmt.executeQuery("SELECT Email FROM customer Where username = '" + name + "'");
121         while(res.next())
122         {
123             output = res.getString(1);
124         }
125         email = output;
126 return email; 
127         }
128          catch (Exception e) {
129             System.out.println(e);
130             return "error";
131         }
132     }
133     
134     public static String getCCName(String name)
135     {
136         try{
137         stmt = con.createStatement();
138         String output = "";
139         String CC = "";
140         
141         ResultSet res = stmt.executeQuery("SELECT CCName FROM customer Where username = '" + name + "'");
142         while(res.next())
143         {
144             output = res.getString(1);
145         }
146         CC = output;
147         return CC; 
148         }
149       
150          catch (Exception e) {
151             System.out.println(e);
152             return "error";
153         }
154     }
155     
156     public static int getCCNumber(String name)
157     {
158         try{
159         stmt = con.createStatement();
160         String output = "";
161         int number = 0;
162         ResultSet res = stmt.executeQuery("SELECT CCNumber FROM customer Where username = '" + name + "'");
163         while(res.next())
164         {
165             output = res.getString(1);
166         }
167         number = Integer.parseInt(output);
168         return number;
169         }
170          catch (Exception e) {
171             System.out.println(e);
172             return -1;
173         }
174     }
175     
176     public static int getMP(String name)
177     {
178         try{
179         stmt = con.createStatement();
180         String output = "";
181         int number = 0;
182         ResultSet res = stmt.executeQuery("SELECT MP FROM customer Where username = '" + name + "'");
183         while(res.next())
184         {
185             output = res.getString(1);
186         }
187         number = Integer.parseInt(output);
188         return number; 
189         }
190          catch (Exception e) {
191             System.out.println(e);
192             return -1;
193         }
194     }
195     
196     public static void setName(String name, String currname) 
197     {
198         /* SQL UPDATE statement required. For instance if rating is 5 and key is "04" then updateStr is
199          UPDATE Libary SET rating = 5 WHERE ID = '04'*/
200         String updateStr = "UPDATE customer SET username = " + name + " WHERE Username = '" + currname + "'";
201         System.out.println(updateStr);
202         try {
203             stmt.executeUpdate(updateStr);
204         } catch (Exception e) {
205             System.out.println(e);
206         }
207     }
208     
209     public static void setEmail(String nEmal, String currname) 
210     {
211         /* SQL UPDATE statement required. For instance if rating is 5 and key is "04" then updateStr is
212          UPDATE Libary SET rating = 5 WHERE ID = '04'*/
213         String updateStr = "UPDATE customer SET Email = " + nEmal + " WHERE Username = '" + currname + "'";
214         System.out.println(updateStr);
215         try {
216             stmt.executeUpdate(updateStr);
217         } catch (Exception e) {
218             System.out.println(e);
219         }
220     }
221     
222     public static void setCCname(String name, String currname) 
223     {
224         /* SQL UPDATE statement required. For instance if rating is 5 and key is "04" then updateStr is
225          UPDATE Libary SET rating = 5 WHERE ID = '04'*/
226         String updateStr = "UPDATE customer SET CCName = " + name + " WHERE Username = '" + currname + "'";
227         System.out.println(updateStr);
228         try {
229             stmt.executeUpdate(updateStr);
230         } catch (Exception e) {
231             System.out.println(e);
232         }
233     }
234     
235     public static void setCCnumber(int number, String currname) 
236     {
237         /* SQL UPDATE statement required. For instance if rating is 5 and key is "04" then updateStr is
238          UPDATE Libary SET rating = 5 WHERE ID = '04'*/
239         String updateStr = "UPDATE customer SET CCNumber = " + number + " WHERE Username = '" + currname + "'";
240         System.out.println(updateStr);
241         try {
242             stmt.executeUpdate(updateStr);
243         } catch (Exception e) {
244             System.out.println(e);
245         }
246     }
247     
248     public static void setMP(int number, String currname) 
249     {
250         /* SQL UPDATE statement required. For instance if rating is 5 and key is "04" then updateStr is
251          UPDATE Libary SET rating = 5 WHERE ID = '04'*/
252         String updateStr = "UPDATE customer SET MP = " + number + " WHERE Username = '" + currname + "'";
253         System.out.println(updateStr);
254         try {
255             stmt.executeUpdate(updateStr);
256         } catch (Exception e) {
257             System.out.println(e);
258         }
259     }
260     
261     public static void deleteAccount(String currname)
262     {
263         try{
264         String output = "";
265         ResultSet res = stmt.executeQuery("SELECT CustomerID FROM customer Where username = '" + currname + "'");
266         while(res.next())
267         {
268             output = res.getString(1);
269         }
270         int number = Integer.parseInt(output);
271         stmt.execute("DELETE FROM SubscribedDeals WHERE CustomerID= " + number);
272         stmt.execute("DELETE FROM Customer WHERE Username= '" + currname + "");
273         }
274         catch (Exception e) {
275             System.out.println(e);
276         }
277     }
278     
279     public static String[] getCustomerDeals(int currnumber)
280     {
281         int i = 0;
282         List<String> dnumbers = new ArrayList<String>();
283         List<String> deals = new ArrayList<String>();
284         try 
285         {
286         ResultSet res = stmt.executeQuery("SELECT DealsID from SubscribedDeals WHERE CustomerID= " + currnumber); //Collect Deal ID's of subscribed deals
287         while (res.next()) 
288             {
289              dnumbers.add(res.getString(1));
290             }
291         
292         System.out.println("here4");
293         String[] dealIDs = new String[dnumbers.size()];
294         dealIDs = dnumbers.toArray(dealIDs);
295         int y = dealIDs.length;
296         for(int x = 0; x < y; x++ )
297         {
298         ResultSet res1 = stmt.executeQuery("SELECT DealName from Deals WHERE DealsID= " + dealIDs[x]); //Translate collected IDs into deal names for display
299         while (res1.next()) 
300             {
301             deals.add(res.getString(1));
302             }
303         }
304         } 
305         catch (Exception e) {
306             System.out.println(e);
307             return null;
308         }
309         String[] sDeals = new String[deals.size()];
310         sDeals = deals.toArray(sDeals);
311         return sDeals;
312     }
313     
314     public static String getDealName(int dNumber)
315     {
316         String output = "";
317         try {
318         ResultSet res = stmt.executeQuery("SELECT DealName from Deals where dealsID= " + dNumber);
319         while (res.next()) 
320             {
321              output = res.getString(1);
322             }
323         } 
324         catch (Exception e) {
325             System.out.println(e);
326             return null;
327         }
328         return output;
329         // return objects array
330     }
331             
332     public static String getDealCost(int dNumber)
333     {
334         String output = "";
335         try {
336         ResultSet res = stmt.executeQuery("SELECT Cost from Deals where dealsID= " + dNumber);
337         while (res.next()) 
338             {
339              output = res.getString(1);
340             }
341         } 
342         catch (Exception e) {
343             System.out.println(e);
344             return null;
345         }
346         return output;
347     }
348     
349     public static String[] getDeals()
350     {
351         List<String> lids = new ArrayList<String>();
352         try {
353         ResultSet res = stmt.executeQuery("SELECT DealName from Deals");
354         while (res.next()) 
355             {
356              lids.add(res.getString(1));
357             }
358         } 
359         catch (Exception e) {
360             System.out.println(e);
361             return null;
362         }
363         String[] deals = new String[lids.size()];
364         deals = lids.toArray(deals);
365         return deals;
366         // return objects array
367     }
368     
369     public static String getDetails(String deal)
370     {
371         String result = "";
372         String output = "";
373         try {
374         ResultSet res = stmt.executeQuery("SELECT DealDesc from Deals where DealName= " + deal);
375         while (res.next()) 
376             {
377              output = res.getString(1);
378             }
379         } 
380         catch (Exception e) {
381             System.out.println(e);
382             return null;
383         }
384         result = output;
385         return result;
386     }
387     
388     public static String getDCost(String deal)
389     {
390         String result = "";
391                 String output = "";
392         try {
393         ResultSet res = stmt.executeQuery("SELECT Cost from Deals where DealName= " + deal);
394         while (res.next()) 
395             {
396              output = res.getString(1);
397             }
398         } 
399         catch (Exception e) {
400             System.out.println(e);
401             return null;
402         }
403         result = output;
404         return result;
405     }
406     
407     public static void subDeal(int cNum, int dealID)
408     {
409         String dName = "";
410         String dCostS = "";
411         float dCost;
412         try{
413         ResultSet res = stmt.executeQuery("SELECT DealName, Cost from Deals where dealsId=" + dealID);
414         while (res.next())
415         {
416             dName = res.getString(1);
417             dCostS = res.getString(2);
418         }
419         }
420          catch (Exception e) {
421             System.out.println(e);
422         }
423         dCost = Float.parseFloat(dCostS);
424         Calendar cal = Calendar.getInstance();
425         Date date = new Date();
426         cal.setTime(date);
427         int month = cal.get(Calendar.MONTH);
428         String updateStr = "INSERT INTO subscribeddeals VALUES (" + cNum + ", " + dealID + ", '" + dName + "', " + dCost + ", " + month + ")";
429         System.out.println(updateStr);
430         try {
431             stmt.executeUpdate(updateStr);
432         } catch (Exception e) {
433             System.out.println(e);
434         }
435     }
436     
437     public static void queryCreation(int customerNumber, String query)
438     {
439         String updateStr = "INSERT INTO query VALUES (" + customerNumber + ", " + query + ")";
440         System.out.println(updateStr);
441         try {
442             stmt.executeUpdate(updateStr);
443         } catch (Exception e) {
444             System.out.println(e);
445         } 
446     }
447     
448     public static List<Deal> getDealsList()
449     {
450         ArrayList<Deal> result = new ArrayList<Deal>();
451         try {
452         ResultSet res = stmt.executeQuery("SELECT DealName, DealsID, DealDesc, Cost, MPCost from Deals");
453         while (res.next()) 
454             {
455              result.add(new Deal(res.getString(1), res.getString(2), res.getString(3), res.getString(4), res.getString(5)));
456             }
457         } 
458         catch (Exception e) {
459             System.out.println(e);
460             return null;
461         }
462         return result;
463     }
464     
465     public static int checkSub(int customerNumber, int dealID)
466     {
467         int result = 0;
468         String output = "";
469         System.out.println("step 1");
470         System.out.println(customerNumber);
471         System.out.println(dealID);
472         try {
473         ResultSet res = stmt.executeQuery("SELECT SubscribedDealsID from subscribeddeals where customerid= " + customerNumber + " AND DealsID= " + dealID);
474         while (res.next()) 
475             {
476               output= res.getString(1);
477             }
478         System.out.println(output);
479         
480         if (!output.equals(""))
481         {
482             System.out.println("not in here");
483             result = 1;
484         }
485         }
486         catch (Exception e) {
487             System.out.println(e);
488             return 1;
489         }
490         
491         return result;
492     }
493     
494      public static List<Order> getCustomerOrders(int customerNumber)
495     {
496         ArrayList<Order> result = new ArrayList<Order>();
497         try {
498         ResultSet res = stmt.executeQuery("SELECT SubscribedDealsID, DealsID, DealName, Dealcost, DateDue from SubscribedDeals where CustomerID=" + customerNumber);
499         while (res.next()) 
500             {
501              result.add(new Order(res.getString(1), res.getString(2), res.getString(3), res.getString(4), res.getString(5)));
502             }
503         } 
504         catch (Exception e) {
505             System.out.println(e);
506             return null;
507         }
508         return result;
509     }
510     
511      public static void createCCPaymentLog(int cID, int ccNumber, String ccName, int dID, int pID, float cost)
512      {
513         Calendar cal = Calendar.getInstance();
514         Date date = new Date();
515         cal.setTime(date);
516         int day = cal.get(Calendar.DAY_OF_MONTH);
517         int month = cal.get(Calendar.MONTH);
518         int year = cal.get(Calendar.YEAR);
519         String updateStr = "INSERT INTO PaymentLog VALUES (" + cID + ", " + dID + ", " + pID + ", " + cost + ", 0, " + ccNumber +", '" + ccName + "', " + day + "" + month + "" + year + " )";
520         System.out.println(updateStr);
521         try {
522             stmt.executeUpdate(updateStr);
523         } catch (Exception e) {
524             System.out.println(e);
525         }
526      }
527      
528       public static void createMPPaymentLog(int cID, int mpCost, int dID, int pID)
529      {
530         Calendar cal = Calendar.getInstance();
531         Date date = new Date();
532         cal.setTime(date);
533         int day = cal.get(Calendar.DAY_OF_MONTH);
534         int month = cal.get(Calendar.MONTH);
535         int year = cal.get(Calendar.YEAR);
536         String updateStr = "INSERT INTO PaymentLog VALUES (" + cID + ", " + dID + ", " + pID + ", 0, " + mpCost + ", 0, 'NONE', " + day + "" + month + "" + year + " )";
537         System.out.println(updateStr);
538         try {
539             stmt.executeUpdate(updateStr);
540         } catch (Exception e) {
541             System.out.println(e);
542         }
543      }
544      
545         public static List<Services> getServicesList()
546     {
547         ArrayList<Services> result = new ArrayList<Services>();
548         try {
549         ResultSet res = stmt.executeQuery("SELECT ServicesID, ServiceName, price, mpcost from Services ");
550         while (res.next()) 
551             {
552              result.add(new Services(res.getString(1), res.getString(2), res.getString(3), res.getString(4)));
553             }
554         } 
555         catch (Exception e) {
556             System.out.println(e);
557             return null;
558         }
559         return result;
560     }
561       
562          public static List<Products> getProductsList()
563     {
564         ArrayList<Products> result = new ArrayList<Products>();
565         try {
566         ResultSet res = stmt.executeQuery("SELECT ProductsID, ProductName, price, mpcost from Products ");
567         while (res.next()) 
568             {
569              result.add(new Products(res.getString(1), res.getString(2), res.getString(3), res.getString(4)));
570             }
571         } 
572         catch (Exception e) {
573             System.out.println(e);
574             return null;
575         }
576         return result;
577     }
578          
579          public static List<Packages> getPackagesList()
580     {
581         ArrayList<Packages> result = new ArrayList<Packages>();
582         try {
583         ResultSet res = stmt.executeQuery("SELECT PackagesID, PackageName, ServiceListID, ProductListID from Packages ");
584         while (res.next()) 
585             {
586              result.add(new Packages(res.getString(1), res.getString(2), res.getString(3), res.getString(4)));
587             }
588         } 
589         catch (Exception e) {
590             System.out.println(e);
591             return null;
592         }
593         return result;
594     }
595         
596     public static void createDeal(String name, int service, int product, float cost, int mpCost)
597     {
598         String resultD = "";
599         String resultP = "";
600         String updateStrP = "INSERT INTO packages VALUES ('" + name + "', " + service + ", " + product + ")";
601         String updateStrD = "INSERT INTO deals VALUES ('" + name + "', 'Customer created deal', " + cost + ", " + mpCost + ")";
602         System.out.println(updateStrP);
603         try {
604             stmt.executeUpdate(updateStrP);
605         } catch (Exception e) {
606             System.out.println(e);
607         } 
608         System.out.println(updateStrD);
609         try {
610             stmt.executeUpdate(updateStrD);
611         } catch (Exception e) {
612             System.out.println(e);
613         } 
614         
615         try {
616         ResultSet res = stmt.executeQuery("SELECT PackagesID from Packages where packagename= '" + name + "'");
617         while (res.next()) 
618             {
619              resultP = res.getString(1);
620             }
621         } 
622         catch (Exception e) {
623             System.out.println(e);
624         }
625         
626         try {
627         ResultSet res = stmt.executeQuery("SELECT dealsID from deals where dealname= '" + name + "'");
628         while (res.next()) 
629             {
630              resultD = res.getString(1);
631             }
632         } 
633         catch (Exception e) {
634             System.out.println(e);
635         }
636         
637         try {
638             stmt.executeUpdate("INSERT INTO PackageList VALUES (" + resultD + ", " + resultP + ")");
639         } catch (Exception e) {
640             System.out.println(e);
641         } 
642     }
643     public static void close() {
644         try {
645             con.close();
646         } catch (Exception e) {
647             // this shouldn't happen
648             System.out.println(e);
649         }
650     }
651 }

#DealBrowser.java

284  private void signUpActionPerformed(java.awt.event.ActionEvent evt) {                                       
285         String selected = (String) dealsCombo.getSelectedItem();
286         int selectedNumber = dealsCombo.getSelectedIndex();
287         int yesorno = 2;
288         int paymentMethod = 0;
289         if(creditRadio.isSelected())
290         {
291             yesorno = showConfirmDialog(null, "Do you want to subscribe to " + deals.get(selectedNumber).getName() + "and paying with Credit Card" );
292             paymentMethod = 1;
293         }
294         else if(mpRadio.isSelected())
295         {
296             yesorno = showConfirmDialog(null, "Do you want to subscribe to " + deals.get(selectedNumber).getName() + "and paying with Membership Points" );
297             paymentMethod = 2;
298         }
299         else
300         {
301             yesorno = 2;
302         }
303         if (yesorno != 2)
304         {
305             int result = ControlList.checkSubscription(selectedNumber);
306             if(result == 0)
307             {
308                 if (paymentMethod == 1)
309                 {
310                 yesorno = showConfirmDialog(null, "You will be charged " + costing.format(deals.get(selectedNumber).getCost()) + " to your credit card, do you want to proceed?" );
311                 if (yesorno == 0)
312                 {
313                 showMessageDialog(null, "Payment was completed successfully and you are now subscribed - DEMO");  
314                 int dealID = deals.get(selectedNumber).getDealNumber();
315                 ControlList.subscribeDeal(dealID, paymentMethod, deals.get(selectedNumber).getCost(), 0);
316                 }
317                 }
318                 else if (paymentMethod == 2)
319                 {
320                 yesorno = showConfirmDialog(null, "You will be charged " + deals.get(selectedNumber).getMPCost() + " MP from your account, do you want to proceed?" );
321                 if (yesorno == 0)
322                 {
323                 showMessageDialog(null, "Payment was completed successfully and you are now subscribed - DEMO");  
324                 int dealID = deals.get(selectedNumber).getDealNumber();
325                 ControlList.subscribeDeal(dealID, paymentMethod, 0, deals.get(selectedNumber).getMPCost()); 
326                 }
327                 }
328             }
329             else
330             {
331                 showMessageDialog(null, "You are already subscribed to this deal!");
332             }
333         }
334     }                                      
335 
336     private void creditRadioActionPerformed(java.awt.event.ActionEvent evt) {                                            
337         // TODO add your handling code here:
338     }                                           
339 
340     private void dealsComboActionPerformed(java.awt.event.ActionEvent evt) {                                           
341         int selected = dealsCombo.getSelectedIndex();
342         dealDetail.setText(deals.get(selected).getDetails());
343         String result = costing.format(deals.get(selected).getCost());      
344         costDisplay.setText(result);
345         mpDisplay.setText(Integer.toString(deals.get(selected).getMPCost()));
346     }                                          
347 
348     private void ccdButtonActionPerformed(java.awt.event.ActionEvent evt) {                                          
349         // TODO add your handling code here:
350         createDeal.createCreateDeal();
351         this.dispose();
352     }   



