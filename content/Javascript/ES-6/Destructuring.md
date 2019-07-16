---
title: Destructuring JavaScript Objects
---

```javascript
const person = {
  firstName: 'Aayush',
  lastName: 'Tuladhar',
  country: 'Nepal',
  twitter: '@aayushtuladhar'
}

/* Problem */
const first = person.firstName;
const last = person.lastName;
console.log(`Hello ${first} ${last}`);

/* Solution */
const {
  firstName,
  lastName
} = person;
console.log(`Hello ${firstName} ${lastName}`);


/* ------------------ */
const art = {
  first: 'ART',
  last: 'Ratna',
  links: {
    social: {
      twitter: 'https://twitter.com/aayushtuladhar',
      facebook: 'https://facebook.com/aayush.tuladhar',
    },
    web: {
      blog: 'https://aayushtuladhar.com'
    }
  }
};

const {
  twitter,
  facebook
} = art.links.social;
console.log(twitter);
console.log(facebook);
```
