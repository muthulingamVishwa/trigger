# When an account is updated, put all related contact emails in the 'List Contact Email' field of the account.
# Answer
## Apex Trigger
```jsx

trigger AccountEmail on Account (before update) {

           AccountUpdate.EmailChange(trigger.newmap);
}

```
## Apex Helper Class

```jsx
Public Class AccountUpdate{

 public Static Void EmailChange(map<id,Account> AccountMap){
      
        map<id,String> AccountEmailList=new map<id,String>();
        
        for(Contact Clist:[select id ,AccountId,Email From Contact Where Email !=null and Accountid =:AccountMap.keyset()]){

                 if(AccountEmailList.containskey(clist.AccountId)){

                AccountEmailList.put(clist.AccountId,AccountEmailList.get(clist.AccountId)+', '+Clist.Email);
            }else{
                AccountEmailList.put(clist.AccountId,Clist.Email);
            }
        }
        
        for(Account acc:Accountmap.values()){

            if(AccountEmailList.containskey(acc.Id)){

                acc.Contact_Email_List__c=AccountEmailList.get(acc.Id);
             
            }
        }

    }
       
}
 ```
