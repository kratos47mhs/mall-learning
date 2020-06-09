In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# IDEA common settings and recommended plugins

>This article mainly records some common settings of IDEA, compares the commonly used shortcut keys of IDEA and Eclipse and recommends some useful plug-ins.

## basic settings

### Set interface style and modify external UI size

![](../images/idea_screen_1.png)

### Set to not reopen the most recent project when opening IDEA
> IDEA will open the most recent project by default. Sometimes we need to select the project we want to open by ourselves. Uncheck this option to achieve it.

![](../images/idea_screen_2.png)

### Set IDEA shortcuts

![](../images/idea_screen_3.png)

### Set code font size

![](../images/idea_screen_4.png)

### Set project file encoding format

![](../images/idea_screen_5.png)

### Set the matching mode for code hints

![](../images/idea_screen_6.png)

### Set the class annotation template of the newly created class file

![](../images/idea_screen_7.png)

## Comparison of common shortcut keys between IDEA and Eclipse

> Friendly reminder: IDEA can be set as Eclipse-style shortcut keys. At File->Settings->Keymap, if you need to change some shortcut keys, you can search according to the English description in the following table and change to the corresponding shortcut keys.

| Eclipse       | IDEA                | English description                      | Explaining                            |
| ------------- | ------------------- | ----------------------------- | ----------------------------------- |
| ctrl+shift+r  | ctrl+shift+n        | Navigate->File                | Find a file in the workspace                    |
| ctrl+shift+t  | ctrl+n              | Navigate->Class               | Find class definition                            |
| ctrl+shift+g  | alt+f7              | Edit->Find->Find Usages       | Find where the method is called. Where the variable is used |
| ctrl+t        | ctrl+t              | Other->Hierarchy Class        | Look at the class inheritance structure                        |
| ctrl+o        | ctrl+f12            | Navigate->File Structure      | Search methods in a class                |
| shift+alt+z   | ctrl+alt+t          | Code->Surround With           | Generate common code blocks                    |
| shift+alt+l   | ctrl+alt+v          | Refactor->Extract->Variable   | Extract variables                            |
| shift+alt+m   | ctrl+alt+m          | Refactor->Extract->Method     | Extraction method                            |
| alt+Left arrow | ctrl+alt+Left arrow | Navigate->Back               | Go back to the previous operating position                  |
| alt+Right arrow | ctrl+alt+Right arrow | Navigate->Forward             | Advance to the previous operating position                  |
| ctrl+home     | ctrl+home           | Move Caret to Text Start      |                         |
| ctrl+end      | ctrl+end            | Move Caret to Text End        |                         |
| ctrl+e        | ctrl+e              | View->Recent Files            | Recently opened files                      |
| alt+/         | ctrl+space          | Code->Completion->Basic       | Prompt variable generation                        |
| ctrl+1        | alt+enter           | Other->Show Intention Actions | Prompt for possible actions                      |
| ctrl+h        | ctrl+shift+f        | Find in Path                  | Global search                            |
| alt+Up/down arrow | alt+shift+Up/down arrow | Code->Move Line Up/Down       | Move a line of code                        |
| ctrl+/        | ctrl+/              | Other->Fix doc comment        | Comment a line of code                     |
| ctrl+alt+s    | alt+insert          | Generate                      | Generate getter, setter, tostring, etc.        |

## Recommended plugin
> Since IDEA itself comes with many plug-ins, which can fulfill most of the needs, here are two plug-ins that I often use.

### Free MyBatis plugin

> The very easy-to-use MyBatis plug-in has a powerful prompt function for MyBatis xml, and can be associated with the mapper interface and the sql implementation in mapper.xml.

#### Can jump from mapper interface and mapper.xml file to each other

![](../images/idea_screen_8.png)

#### Various tips in mapper.xml

![](../images/idea_screen_9.png)
![](../images/idea_screen_10.png)

### Lombok plugin

> Lombok adds very interesting additional features to the Java language. You can no longer have to write getter, setter and other methods for the entity class, you can have it through a comment.

A class without getter and setter methods has getter and setter methods by adding @Getter and @Setter annotations.

![](../images/idea_screen_11.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
