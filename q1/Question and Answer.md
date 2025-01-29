#Apex trigger to prevent users from assigning contact roles to closed opportunities and to avoid assigning duplicate contact roles on the same opportunity(Asked in an interview).

## Answer
```jsx


trigger preventuser on OpportunityContactRole (before insert) {
   
switch on trigger.operationtype{

 when Before_insert{
     
    prevent.userContactrole(trigger.new);

     }
}
}


//methods
public class prevent{

 public static void userContactrole(list<OpportunityContactRole> listcontrolrole){

      set<id> setid=new set<id>();

        for(OpportunityContactRole opp:listcontrolrole){
               setid.add(opp.opportunityId);
              
}
map<id,set<id>> mapid=new map<id,set<id>>();

        for(OpportunityContactRole ort: [select id,OpportunityId,ContactId From OpportunityContactRole where OpportunityId in :setId ]){
        
           if(!mapid.containskey(ort.OpportunityId)){
               
           mapid.put(ort.OpportunityId,new set<id>{ort.ContactId});
        }else{
        
          mapid.get(ort.OpportunityId).Add(ort.ContactId);
           
        }
          
}
    set<id> newid=new set<id>();
     
       for(Opportunity opp:[select id from Opportunity where StageName like 'Close%'and id in:setId]){
           newid.add(opp.id);
}

 for(OpportunityContactRole oppp:listcontrolrole){
    if(newid.contains(oppp.OpportunityId)){
        oppp.addError('This Opporunity is close');
}else if(mapid.get(oppp.OpportunityId).Contains(oppp.ContactId)){
      oppp.addError('this contact and opporunity already have Contacl role');
}
}
    
}
}
```
