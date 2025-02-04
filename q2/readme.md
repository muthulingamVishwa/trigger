# When an account is updated, put all related contact emails in the 'List Contact Email' field of the account.(Asked in an interview)
# Answer
## Apex Trigger
```jsx

trigger AccountEmailupdated on Account (before update) {

           AccountUpdate.ListContactEmail(trigger.newmap);
}

```
## Apex Helper Class

```jsx
Public Class AccountUpdate{

 public Static Void ListContactEmail(map<id,Account> AccountMap){
      
        map<id,String> ContactEmailList=new map<id,String>();
        
        for(Contact Clist:[select id ,AccountId,Email From Contact Where Email !=null and Accountid =:AccountMap.keyset()]){

                 if(ContactEmailList.containskey(clist.AccountId)){

                ContactEmailList.put(clist.AccountId,ContactEmailList.get(clist.AccountId)+', '+Clist.Email);
            }else{
                ContactEmailList.put(clist.AccountId,Clist.Email);
            }
        }
        
        for(Account acc:Accountmap.values()){

            if(ContactEmailList.containskey(acc.Id)){

                acc.Contact_Email_List__c=ContactEmailList.get(acc.Id);
             
            }
        }

    }
       
}
 ```
