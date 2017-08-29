# json-cv
A specification for the standardization of CV data using JSON format


## Why?
At Whiteboard (and pretty much every other company), experience matters. Having an updated resumé is a major step towards legitimizing your experience to benefit your career.

However, few tools exist that standardize this information. In general, tracking large life phases is supported by social tools. But wouldn't it be cool if we could have a single way of representing this information and easily transfer it from one place to the other?

This document outlines an attempt to standardize the CV JSON data format.

## Ground rules

- Valid JSON only
- All appropriate identifying keys should use `camelCase`

## Structure
The structure of the JSON object follows these guidelines.

The top-level object is representative of a single person. Thus, no person should have resumé data that is represented by more than one top-level object. Top level keys should all correspond to either objects or arrays.

A person object starts with personal data.

```
{
  "personalData": {
    "firstName": "Jonathan",
    "lastName": "Cutrell",
    "prefix": null,
    "suffix": null,
    "birthdate": "1988-12-29",
    "sex": "male"
  }
  // ...
}
```

In the above object, all values are identified as open field strings except for the birthdate, which follows the international standard `YYYY-MM-DD`. All dates in json-cv should follow this format for optimum parsing compatibility. Note that identifying a month can be accomplished by using only the first day of the month, i.e. `2017-08-01`. The client can determine how to display this information, and as we'll see, when date ranges are provided, they will identify by a `resolution` parameter (to know what pieces of the date information can be discarded).

You may be wondering, "Where is my address?" - because the object represents a person, we don't want to put any data into the `personalData` object with anything other than data that is essentially static. Additionally, we want to provide the option of capturing location over a historical period. Thi sis how that would look.

```
{
  "personalData": {
    // ...
  },
  "location": [
    {
      "street": "123 Anywhere Lane",
      "postalCode": "22334",
      "city": "Chattanooga",
      "state": "Tennessee",
      "country": "United States",
      "datePeriod": {
        "start": "2013-07-01",
        "end": null,
        "resolution": "month"
      }
    },
    {
      "street": null,
      "postalCode": null,
      "city": "Atlanta",
      "state": "Georgia",
      "country": "United States",
      "datePeriod": {
        "start": "2011-04-01",
        "end": "2013-06-01",
        "resolution": "month"
      }
    }
  ]
}
```

Notice that the location is an array. The order of this array is not necessarily specific, as the most important ordering information will be found in the date ranges. This allows for overlapping locations (for example, if someone commuted between two locations during an overlap period). The only required fields for location are `city`, `state`, and `country`. The location objects are considered to be valid on their own without a `datePeriod`, however, for any reasonable system that generates a timeline, a `datePeriod` becomes imminently important.

A given `datePeriod` object contains a "start" and "end" key, as well as a resolution identifier. (By default, all date resolutions should be parsed as "day"). If the end value is not identified, client systems should reasonably assume that the location identifier is continuous into the present time.

```
{
  "personalData": {
    // ...
  },
  "location": [
    // ...
  ],
  "experience": [
    {
      "type": "education",
      "institution": "Georgia Tech",
      "title": "M.S. Digital Media",
      "description": "Some description of what this experience was like and required.",
      "datePeriod": {
        //...
      }
    },
    {
      "type": "award",
      "institution": "Georgia Tech",
      "title": "Student of the Year",
      "description": "Some description of what this experience was like and required.",
      "datePeriod": {
        //...
        "start": "2013-01-01",
        "end": "2013-12-01",
        "resolution": "year"
      }
    },
    {
      "type": "work",
      "institution": "Whiteboard",
      "title": "CTO",
      "description": "Some description of what this experience was like and required.",
      "datePeriod": {
        //...
        "start": "2010-05-01",
        "end": null,
        "resolution": "month"
      },
      "meta": {
        // user-defined fields for capturing additional information
      }
    }
  ]
}
```
