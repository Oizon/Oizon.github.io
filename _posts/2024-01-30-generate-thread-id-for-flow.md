---
layout: post
author: William Galinat
tags: [Salesforce, Invocable Action, Flow]
---
# 🚀 Elevate Your Salesforce Flow: The GenerateThreadIdAction Journey! 🌐

In the Salesforce universe, navigating the cosmos of case routing can be a thrilling quest. While Lightning Email Templates offer their magic, sometimes you need that extra spark for flow integration. Enter the hero of our story – the GenerateThreadIdAction! 🦸‍♂️

## Table of Contents
- [Why Choose GenerateThreadIdAction?🤔](#why-choose-generatethreadidaction🤔)
- [Unveiling the Magic Sctipt✨](#unveiling-the-magic-sctipt✨)
- [Features That Will Make You Smile 😃](#features-that-will-make-you-smile-😃)
- [How to Use GenerateThreadIdAction in Your Flow](#how-to-use-generatethreadidaction-in-your-flow)
- [Testing the Waters: A Symphony of Assurance🎵](#testing-the-waters-a-symphony-of-assurance🎵)
- [Meet the Guardians of Quality Code 🛡️](#meet-the-guardians-of-quality-code-🛡️)
- [Key Notes from the Testing Symphony 🎼](#key-notes-from-the-testing-symphony-🎼)
- [In Conclusion: A Note of Confidence 🎤](#in-conclusion-a-note-of-confidence-🎤)

## Why Choose GenerateThreadIdAction?🤔

Salesforce offers a treasure trove of tools, but when the standard fare won't cut it, out invocable action swoops in like a superhero to save the day!💪 The GenerateThreadIdAction taps into the mighty EmailMessages.getFormattedThreadingToken method to wield the power of ThreadId in flows.

## Unveiling the Magic Sctipt✨
```
public with sharing class GenerateThreadIdAction {
    @InvocableMethod(
        label='Generate ThreadId for Case' 
        description='Uses the apex method EmailMessages.getFormattedThreadingToken for flows.'
        iconName='slds:standard:record_lookup'
        category='Email'
    )
    public static List<ThreadIdResults> getThreadIdForCase(List<Id> caseIds) {
        List<ThreadIdResults> results = new List<ThreadIdResults>();
        for(Id caseId: caseIds){
            ThreadIdResults result = new ThreadIdResults();
            result.threadId = EmailMessages.getFormattedThreadingToken(caseId);
            results.add(result);
        }
        return results;
    }

    public class ThreadIdResults{
        @InvocableVariable(label='ThreadId')
        public String threadId;
    }
}
```

## Features That Will Make You Smile 😃

1. Invocable Magic: Our hero, the getThreadIdForCase method, is now your trusty sidekick, accessible in the flow universe. 🧙‍♂️
2. Threading Token Bliss: Unleash the power of EmailMessages.getFormattedThreadingToken to snatch the ThreadId for each case with flair. 🎯
3. Results Extravaganza: The nested class ThreadIdResults comes with an invocable variable threadId to hold the golden treasures. 📊

## How to Use GenerateThreadIdAction in Your Flow

1. Copy the provided Apex class into your Development Salesforce environment. 🚚
2. In your Salesforce setup, navigate to Flows and create a new flow. 🎨
3. Add this custom action to your flow from the canvas
4. Configure the action by providing the necessary Case Id(s) as input. ⚙️
5. Use the output variable in your flow to retrieve the generated ThreadId for further processing. 🎉

## Testing the Waters: A Symphony of Assurance🎵
No Salesforce adventure is complete without a reliable guide to navigate the testing landscape. Fear not, for the GenerateThreadIdAction comes prepared with its own anthem of assurance – the Test Class! 🎶

## Meet the Guardians of Quality Code 🛡️
```
@isTest
private class GenerateThreadIdActionTest {
    
    @isTest
    static void testGenerateThreadIdForCase() {
        // Test data setup
        Case testCase = new Case(Subject = 'Test Case');
        insert testCase;

        List<Id> caseIds = new List<Id>{ testCase.Id };

        // Call the action
        List<GenerateThreadIdAction.ThreadIdResults> results = GenerateThreadIdAction.getThreadIdForCase(caseIds);

        // Assert the outcome
        System.assertEquals(1, results.size(), 'Expected one result');
        System.assertNotEquals(null, results[0].threadId, 'ThreadId should not be null');
    }
}
```
## Key Notes from the Testing Symphony 🎼
1. Data Set the Stage: The test class sets up a test Case, ensuring a playground for the action's performance. 🎭
2. Action Unleashed: The GenerateThreadIdAction.getThreadIdForCase is called, where the magic unfolds. 🌟
3. Outcome Harmonized: Assertions ensure that the ThreadId is crafted and delivered successfully. 🎵

## In Conclusion: A Note of Confidence 🎤

With the GenerateThreadIdAction and its loyal Test Class by your side, your Salesforce symphony echoes with confidence. Embrace the testing journey as an integral part of your coding saga, ensuring that your solutions not only shine but stand the test of time. 🏆 Let the GenerateThreadIdAction be your melody of success in the grand symphony of Salesforce development! 🚀