When inserting new Leads, the system needed to identify duplicates based on the Email field. If a duplicate was found, 
the new Lead had to be marked as "Duplicate" and linked to the Original Lead. This should also work for Converted Leads too.
Note:- the oldest Lead in the system (based on the CreatedDate) should be identified as Original Lead.

---
# trigger class
```jsx
trigger duplicate on lead(before insert){

      switch on Trigger.operationtype{
    
                when before_insert{   
                     leadHandler.dupilcatehandler(trigger.new);            
              }
      } 
}
 
```
# class 
leadHandler
---
```jsx

 public class leadHandler{

   public static void dupilcatehandler(list<lead> NewListLead){
             
   map<string,lead> oldLeadCreated=originalLead.getoriginalLead(NewListLead);
       
    for(lead org:NewListLead){
            if(oldLeadCreated.containsKey(org.Email)){
               org.Original_Lead__c=oldLeadCreated.get(org.Email).Id;
               org.Status='Duplicate';
            } 
        
        
    }
  }
}
```
# class 
oldLeadCreated
```jsx
public class originalLead {
    public static map<string,lead> getoriginalLead(list<lead> NewListLead){

           list<string> emaillist=new list<string>();
                     for(lead ld:NewListLead){
                          if(ld.Email != null){
                              emaillist.add(ld.Email);
                           }
                       }
   
         map<string,lead> oldLeadCreated=new map<string,lead>();
        for(lead oldlead:[select id ,email,CreatedDate from lead where email =:emaillist]){
     
              if(oldLeadCreated.containsKey(oldLead.Email)){
                   if(oldLead.CreatedDate < oldLeadCreated.get(oldLead.Email).CreatedDate){
                              oldLeadCreated.put(oldLead.Email,oldLead);
                      }
               }else{
                oldLeadCreated.put(oldLead.Email,oldLead);
            }
   }    
return oldLeadCreated;

  }
}
 
```

