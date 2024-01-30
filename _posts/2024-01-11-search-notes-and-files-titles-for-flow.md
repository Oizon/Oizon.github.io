---
layout: post
author: William Galinat
tags: [Salesforce, Invocable Action, Flow]
---

# 🚀 Navigating the Coding Cosmos: The Journey of GetContentDocumentAction Class! 🚀

Ever felt like you're on a coding odyssey, trying to decipher the mysteries between two points in your code? 🧐 Let's embark on a quick adventure using our GetContentDocumentAction class as our guide!
🗺️ Our mission: To determine if a Contact has a file attached with the illustrious title resume. 🌟
## Table of Contents
- [🤖 First challenge: The commented-out iterations! 🤖](#🤖-first-challenge-the-commented-out-iterations-🤖)
- [🕵️ Next up: The InvocableMethod! 🕵️](#🕵️-next-up-the-invocablemethod-🕵️)
- [🌪️ Code Whirlwind: Bulkifying for Better Performance! 🌪️](#🌪️-code-whirlwind-bulkifying-for-better-performance-🌪️)
- [🛠️ Refactoring Magic: Enter the Request Class! 🛠️](#🛠️-refactoring-magic-enter-the-request-class-🛠️)
- [🚀 Expanding Horizons: Beyond Resume Checks! 🚀](#🚀-expanding-horizons-beyond-resume-checks-🚀)


## 🤖 First challenge: The commented-out iterations! 🤖
It's like exploring alternate universes within your code! We're considering various paths, but remember, those iterations are just like stepping stones—essential during development but removed before entering the production galaxy. ⚙️✨

```
private static final Set<String> ALLOWED_TITLES = new Set<String>{
    '%resume%','%Resume%', '%resum%'
    };
    @InvocableMethod(label='Check for resume File on Contact'
                    description ='Checks for a file on the contact record. WARNING: ONLY CHECKS FOR TITLE, NOT CONTENT!'
                    category= 'Custom Action'
                    iconName='slds:standard:search')
    public static List<Boolean> checkContactForFile(List<Id> contactIds) {
    public static List<List<ContentDocument>> checkContactForFile(List<Request> requests){
        Map<Id,Id> contactIdToDocumentLink = new Map<Id,Id>();


        Set<Id> idsToCheck = new Set<Id>();
        Map<Id, ContentDocumentLink> contactToconDocLinkMap = new Map<Id, ContentDocumentLink>();
        for(ContentDocumentLink conDocLink : [  
            SELECT Id, ContentDocumentId, LinkedEntityId 
            FROM ContentDocumentLink 
            WHERE LinkedEntityId IN :contactIds
            WITH USER_MODE
            ]){
            idsToCheck.add(conDocLink.ContentDocumentId);
            contactToconDocLinkMap.put(conDocLink.LinkedEntityId, conDocLink);
            contactIdToDocumentLink.put(conDocLink.LinkedEntityId, conDocLink.ContentDocumentId);
        }
        List<ContentDocument> contentOnContact = [
            SELECT Id, Title 
            FROM ContentDocument 
            WHERE Title LIKE :ALLOWED_TITLES 
            AND Id IN :idsToCheck
            WITH USER_MODE
        ];
        Map<Id, ContentDocument> conDocIdMap = new Map<Id, ContentDocument>();
        for(ContentDocument conDoc: contentOnContact){
        }
        List<Boolean> booleanReturnList = new List<Boolean>();
        //Changed from .size() > 0 to ! isEmpty()
        for(Id contactId : contactIds){
            ContentDocumentLink conDocLink = contactToconDocLinkMap.get(contactId);    
            booleanReturnList.add(idsToCheck.contains(contactId));
        }
        return booleanReturnList;
```
## 🕵️ Next up: The InvocableMethod! 🕵️
In our quest, we've labeled it 'Get File By Name'—a method to search the given ID for Content Documents by name. A handy tool when seeking specific data in the vast coding cosmos! 🌌📂
```
@InvocableMethod(
        label='Get File By Name'
        description = 'Searches the given Id for Content Documents by name.'
        category= 'Custom Action'
        iconName= 'slds:standard:record_lookup'
    )
    public static List<List<ContentDocument>> checkContactForFile(List<Request> requests){
```
## 🌪️ Code Whirlwind: Bulkifying for Better Performance! 🌪️
Watch out for the bulkified version! We've harnessed the power of sets and maps to handle multiple records seamlessly. No more worries about ghostly encounters from Astro past haunting your system! 👻🚀
```
public static List<List<ContentDocument>> checkContactForFile(List<Request> requests){
        Set<Id> idsToSearch = new Set<Id>();
        Set<String> allowedTitles = new Set<String>();
        for(Request req: requests){
            idsToSearch.add(req.sObjectId);
            for(String term: req.searchTerms){
                String expandSearch = '%' + term + '%';
                allowedTitles.add(expandSearch);
            }
        }
        Map<Id,ContentDocumentLink> idToContentDocumentLink = new Map<Id,ContentDocumentLink>();
        Set<Id> idsToCheck = new Set<Id>();
        for(ContentDocumentLink conDocLink : [  
            SELECT Id, ContentDocumentId, LinkedEntityId 
            FROM ContentDocumentLink 
            WHERE LinkedEntityId IN :idsToSearch
            WITH USER_MODE
            ]){
                idsToCheck.add(conDocLink.ContentDocumentId);
                idToContentDocumentLink.put(conDocLink.ContentDocumentId, conDocLink);
        }
        List<ContentDocument> contentOnContact = [
            SELECT FIELDS(Standard) 
            FROM ContentDocument 
            WHERE Title LIKE :allowedTitles 
            AND Id IN :idsToCheck
            WITH USER_MODE
        ];
        Set<Id> contactIdsWithFile = new Set<Id>();
        for(ContentDocument conDoc: contentOnContact){
            ContentDocumentLink conDocLink = idToContentDocumentLink.get(conDoc.Id);
            contactIdsWithFile.add(conDocLink.LinkedEntityId);
        }
        Map<Id, List<ContentDocument>> contentDocumentToReturnMap = new Map<Id, List<ContentDocument>>();
        for(ContentDocument conDoc: contentOnContact){
            ContentDocumentLink conDocLink = idToContentDocumentLink.get(conDoc.Id);
            if(contentDocumentToReturnMap.keySet().contains(conDocLink.LinkedEntityId)){
                contentDocumentToReturnMap.get(conDocLink.LinkedEntityId).add(conDoc);
            }else {
                List<ContentDocument> contDocListForMap = new List<ContentDocument>();
                contDocListForMap.add(conDoc);
                contentDocumentToReturnMap.put(conDocLink.LinkedEntityId, contDocListForMap);
            }    
        }
        List<List<ContentDocument>> contentDocumentToReturn = new List<List<ContentDocument>>();
        for(Request req: requests){
            contentDocumentToReturn.add(contentDocumentToReturnMap.get(req.sObjectId));
        }
        return contentDocumentToReturn;
    }
```
## 🛠️ Refactoring Magic: Enter the Request Class! 🛠️
Our Request class brings structure to chaos. 'Record ID to Search' and 'Search Terms Collection' act as guiding beacons, making our code more legible and robust! 🌐📦
```
public class Request {
        @InvocableVariable(
            label = 'Record ID to Search.'
            required = true
            description = 'The unique identifier of the record to be searched. Avoid using this in loops to prevent unwanted performance issues.' 
        )
        public Id sObjectId;
        @InvocableVariable(
            label = 'Search Terms Collection'
            required = true
            description = 'A collection of variable type text to be used for searching relevant files. Each term will be considered during the search.'
        )
        public List<String> searchTerms;
    }
```
## 🚀 Expanding Horizons: Beyond Resume Checks! 🚀
Now, the magic doesn't end with Resumes! With GetContentDocumentAction, you can extend this cosmic power to check if ANY record has attached files. 🌠✨ Imagine the possibilities—from Contacts to any record in your Salesforce universe, ensuring files are at your fingertips! 🚀🌌
May your coding journeys be as thrilling as our quest through GetContentDocumentAction! 🚀👩💻 Happy coding, fellow space explorers! 🌌🚀