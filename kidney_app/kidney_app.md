# Kenny the Kidney: a mobile health application

## Introduction

An increasing number of mobile health (mHealth) applications are being developed, and there are currently several mHealth apps that address a variety of health topics and diseases. A study published by Biomedical Informatics in 2014 has shown mHealth applications are a valuable tool to contribute to patient empowerment and better self-management of chronic diseases.

In one of my graduate course, my team proposed an application that will serve as a central repository for dialysis patients to track more than just their nutritional intake. This App will be a more comprehensive repository of data and resources for dialysis patients, which will enable them to become more educated and successful in managing their disease over time. I was honoured to become the so-called chief full-stack developer for this project. 

## Main Problems

How to deliver a full-stack App with only one developer?

How to provide Accurate visual display and comprehensible notification concerning eGFR and overall renal function

## Solutions I applied

I chose microservices, a variant of the service-oriented architecture (SOA) structural style, to arrange our Application as a collection of loosely coupled services.

![System Architecture Design](https://drive.google.com/uc?id=1X-VIh9peh_T0AFxBW2GBW7hBERXQAjV8 "System Architecture Design")

The services include hosting, sending email for doctor verification, doctor confirmation and warning for patient condition. All components interact via their REST API.

### Framework: Angular

Angular is a platform for building mobile and desktop web applications. It has all the library to achieve the functions we need. For example,

1. It has [AngularFire](https://github.com/angular/angularfire), an official Angular library for Firebase, which has off-the-shelf Authentication support.

1. It has [Angular Material](https://material.angular.io/), which offers straightforward APIs for UI with consistent cross-platform behaviours.

1. It has [Angular Animations utility library](https://github.com/filipows/angular-animations), which provides reusable and parametrized animations that can be used in a declarative manner.

### Front-end: Firebase Hosting

The front-end of our Web App is backed by Firebase Hosting, a production-grade web content hosting. The front-end doesn't keep state, and all the data are saved in our database in real-time.

Using the Firebase CLI, I deployed static content that complied by Angular, from local directories on our computers to the hosting server. 

All content is served over an SSL connection from the closest edge server on Google's global CDN.

### Database: Firestore

I leveraged the real-time listeners and offline support provided by Firestore to keep the data in-sync across client Apps. Also, in this way, our App can work regardless of network latency or Internet connectivity. Moreover, after writing data into this database, it can trigger other servers, like "doctor verification". 

This database is document-oriented. I designed specific schemas for "doctor", "eGFR", "user" and "weight" datasets to fit this feature. For example, the schema of eGFR data implemented by Typescript is as follow. Other schemas can be found at [Models](https://github.com/Moo-YewTsing/ngKK/tree/master/src/App/services/models) in our Git Repo.

## Result

![mobile screens](https://drive.google.com/uc?id=1PoFHQj0mKweR1TCJUUGxq_10MyOz5RN- "mobile screens")

Here, we show images on mobile screens. The web pages will render differently on a variety of devices and window or screen sizes. Because the images on large screens are, of course, large, we only show the home page on large screens as the following.

![Home Page for Large Screens](https://drive.google.com/uc?id=1bA_DhDah9A6-ZMP9npXWOaZ2ekO3bwFH "Home Page for Large Screens")

Our App is responsive, i.e. we made web pages render well on a variety of devices and window or screen sizes. For example, when users view the home page on a large screen, the left-side navigation bar and the legend of charts appear by default, which is hidden on small screens.


### Service-1: Alert

If the patient has a verified doctor and enables the notification function of our App, when the eGFR is in the range of 'Kidney function moderately to severely decreased', 'Kidney function severely decreased' or 'Kidney failure', an Https call will be sent from the user's device to the "Alert" service,  automatically.

This service will retrieve the recent data from our database and call a third party API from [QuickChart](https://quickchart.io) to create a chart for the data. This service will attach the chart and data in an email and send the email to the corresponding doctor.

![Kidney failure](https://drive.google.com/uc?id=14XseFU3m-z986yrwH-tCh5ssaRe40MVY "Kidney failure")

![Alert](https://drive.google.com/uc?id=1hRcP-w68apX9zhvCCTg0Tlva5RGGCisK "Doctor receive a warning")

![data](https://drive.google.com/uc?id=1Au55oQzk6ckjRDAFFQGR1fqUyVIVVevy "Attach the data in an email")

### Service-2: Doctor Confirmation
 
If users assign random people as their doctors, without "Doctor Confirmation" service to let the receivers know they will get Alert emails, the unlucky people chosen by the bad users may be flooded by alert emails sent from our Alert service.

Doctor Confirmation will prevent users from abusing our App in this way. When users add their doctors and enable the notification function, Doctor Confirmation service will send an email to the possible doctor to ask their confirmation. This email includes a link which can send a "POST" call to the Doctor Verification service. Only after the doctor confirm they are willing to get alerting emails for their patients, the alerting emails will be sent to them.

![Confirmation](https://drive.google.com/uc?id=1Ge70IgDeZ18d57l1JRdlP8X3I2eJDcOB "Avoid abuse")

![Send Doctor Verification](https://drive.google.com/uc?id=1oBntbvlY2enYGrqS-5R4JwOgTEyHOJ-8 "A verification email")

### Service-3: Doctor Verification

This service is called from confirmation emails only. When it's triggered, it will write the "doctorVerified" in patient's profile as "Ture", so that when the patient's eGFR is in dangerous range, an alter email can be sent to the doctor.

![Doctor Verification](https://drive.google.com/uc?id=1hv2KyIK10sZEHRagkI3w-zRmm1qyO-xl "Doctors know Verification is done")

![Patient know Verification](https://drive.google.com/uc?id=1ceh49vUE058YkTeMFH8JGCkdtKwp-Gom "Patients know Verification is done")

