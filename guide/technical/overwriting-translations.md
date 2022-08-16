# Guide: Overwriting Translations

It is possible to overwrite the standard translations or even to add a completely custom
translation, e.g. for an unsupported language. This is an advanced feature that requires a little
more technical skill. This guide will show you how it's done.

## Introducing the format – What is JSON?

JSON is a markup language that is commonly used for configuration files and localization. This is
because it is a simple format to read and write and is also very compact. There are, however, a
few minor challenges.

Let's just start with an example:

```json
{
  "apples": 2,
  "bananas": 5,
  "peaches": 3
}
```

The above is a simple JSON file with three key-value pairs. Note, that the quotation marks around
the key are required. Now, aside from numbers, there can also be strings. Strings should also be
in quotes. Make sure, to use double quotes (`"`) not the typographically correct quotes (`“”`).

```json
{
  "chocolate": "As much as you want!"
}
```

Also, the key-value pairs are surrounded by curly brackets (`{}`). These can be considered objects
or levels. A JSON file can only have one root object.

Between key-value pairs is always a comma!

Next up: Nesting. This allows us to group options or strings together and give structure to our
documents.

```json
{
  "fruit": {
    "apples": 2,
    "bananas": 5,
    "peaches": 3
  },
  "sweets":  {
    "chocolate": "As much as you want!"
  }
}
```

Now we have two objects / groups. The items inside a group are indented with spaces. Let's add
another level:

```json
{
  "fruit": {
    "regional": {
      "apples": 2,
      "peaches": 3
    },
    "remote": {
      "bananas": 5
    }
  },
  "sweets": {
    "chocolate": "As much as you want!"
  }
}
```

Note, that the last key-value pair in an object is not allowed to be followed by a comma!

We can add as many levels, as we need. Grouping items like this is not only helpful for readability
and organisation, programmers can also easily navigate this structures to find the value they were
looking for by using the dot-notation. For example, if I wanted to know how many peaches to get, I
could describe the "path" like so: `fruit.regional.peaches` and get `3` in return.

Now, JSON can sometimes be a bit tricky. Should you experience problems with your JSON formatting,
you can validate your file using something like <https://jsonlint.com//>.

## The supported languages

Currently, the following languages are officially supported:

- German
- English

The following translations were provided by the community:

- Arabic (ar)
- Azerbaijani (az)
- Bengali (bn)
- Chinese Traditional (zh-TW)
- Croatian (hr)
- Greek (el)
- Hausa (ha)
- Hebrew (he)
- Hindi (hi)
- Hungarian (hu)
- Indonesian (id)
- Italian (it)
- Japanese (ja)
- Kinjarwanda (rw)
- Korean (ko)
- Malayalam (ml-IN)
- Persian (fa)
- Polish (pl)
- Portuguese, Brazilian (pt-BR)
- Romanian (ro)
- Russian (ru)
- Serbian, Cyrillic (sr)
- Slovak (sk)
- Spanish (es-ES)
- Swahili (sw)
- Turkish (tr)
- Ukranian (uk)
- Vietnamese (vi)

### So how do you actually overwrite translations?

Every language that is supported has it's own JSON file on GitHub. Actually, there are two files
for every language:

- [OpenElectionCompass UI](https://github.com/open-election-compass/ui/tree/master/src/locales)
  (a collection of generic UI components)
- [OpenElectionCompass Client](https://github.com/open-election-compass/client/tree/master/src/locales)
  (the core software; this is most likely what you are looking for)

If you want to create a complete custom language, you will need both. If you just want to overwrite
a few strings, you don't have to copy everything. Let's take a look at an example: The big green
button that pops up at the bottom is called the GuideButton and it reads `Learn how this works` in
the beginning. If I want to overwrite that with, say `Get started!`, I would need to do this:

1. Find the correct JSON file for the language I want to overwrite. Click the links above and take
   a look at `en.json` on GitHub.
2. Use the search to find the occurence of `Learn how this works` in that file.

```json
{
  "elements": {
    # ... there are some lines in between, but 'guide-button' is a "child" of 'elements', so don't overlook this!
    "guide-button": {
      "start": "Let's go",
      "introduction": "Learn how this works",
      "first-thesis": "Start with the first thesis",
      "thesis": "Proceed to the next thesis",
      "party": "Select the parties",
      "match": "See your result",
      "compare": "Read the statements"
    }
  }
}
```

3. This seems to be the right place. Now note the path: `elements.guide-button.introduction`. We want to
   overwrite the value at that path.
4. Now, in the Configuration Editor, open the English language by clicking 'Edit' and look for the
   Overwrites field. We're going to paste some JSON there:

```json
{
  "elements": {
    "guide-button": {
      "introduction": "Get started!"
    }
  }
}
```

5. Note, that you need to replicate the structure, but you can omit all the keys and values that you
   don't want to overwrite.

### Some translations contain "weird" characters

You might encounter strings like this one:

```json
{
  "elements": {
    "kiosk-mode": {
      "description": "The election compass will be reset now and all your answers will be deleted. | The election compass will be reset in {count} seconds and all your answers will be deleted. | The election compass will be reset in {count} second and all your answers will be deleted."
    }
  }
}
```

There are two things going on here:

- The string is repeated and the repititions are seperated by a `|`. These are pluralizations. The
  system will pick the correct pluralization automatically. Just make sure to keep the lines `|` and
  surrounding spaces.
- The strings contain curly brackets: `{count}`. As you might guess, this is a placeholder and will
  be replaced with something else by the system. In this case, a number. You can move placeholders
  around in a sentence, but don't delete them and don't change or translate them or the system won't
  recognize them.

In general, just try to keep structures like these intact ;)

## Adding a custom language

If you need to, you can use the described method to overwrite all strings to get a completely custom
language. Simply copy all contents from a language and use that as a base for your own translation.

We're always looking to expand support for languages. If you would like to share your translations,
please get in touch!