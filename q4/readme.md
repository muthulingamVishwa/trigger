#  Write a trigger that:
Creates a task when the Opportunity stage is changed to ‘Qualification’.
Deletes the existing task when the Opportunity stage is changed to ‘Proposal/Price Quote’.
---
## Answer

### Apex Trigger
```jsx

trigger opportunityTrigger on Opportunity (after update) {

switch on trigger.operationtype{

 when after_update{
     
     opportunityHandler.opportunityTotask(trigger.new,trigger.oldmap);

     }
   }
}

```
### Apex Helper Class
```jsx
public class opportunityHandler {
    
    public static void opportunityTotask(list<Opportunity> listopportunity,map<id,Opportunity> oldmapopp){
        
        set<opportunity> created=new set<opportunity>();
        set<id>  deletedTask=new set<id>();
        for(Opportunity op : listopportunity){
            if(op.StageName != oldmapopp.get(op.Id).StageName && op.StageName =='Qualification'){
                created.add(op);                 
            }else if(op.StageName != oldmapopp.get(op.Id).StageName && op.StageName =='Proposal/Price Quote')
               deletedTask.add(op.Id);
        }
        if(!created.isEmpty()){
            taskcreated(created);
        }
          if(!deletedTask.isEmpty()){
            taskDelete(deletedTask);
        }
        
       
    }
    
        public static void taskcreated(set<opportunity> listopp){
        
          list<Task> listTask=new list<Task>();
        
            for(opportunity op: listopp){
               task tasks=new task();
                tasks.Subject=op.Name+'-'+op.StageName;
                tasks.ActivityDate=op.CloseDate;
                tasks.WhatId=op.Id;
                tasks.OwnerId=op.LastModifiedById;
                tasks.Status='Not Started';
                listTask.add(tasks);
            }
            
            if(!listTask.isEmpty()){
                insert listTask;
            }
        
    }
    
    public static void taskDelete(set<id> listId){
        
        
        list<task> listtask=[select id,whatId from task where whatId =:listId];
        
        if(!listtask.IsEmpty()){
            delete listTask;
        }
    }
    

}
```
