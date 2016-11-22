# Tabular Editor
Tabular Editor is a tool that lets you easily manipulate and manage measures, calculated columns, display folders, perspectives and translations in SQL Server Analysis Services Tabular Models (from Compatibility Level 1200 and onwards). The tool is written entirely in .NET WinForms (C#).

* [Download Tabular Editor v. 2.0](https://github.com/otykier/TabularEditor/releases/latest)

### Prerequisites
Tabular Editor uses the [Tabular Object Model](https://msdn.microsoft.com/en-us/library/mt706505.aspx) to load and save metadata to and from Model.bim files or existing databases. For this reason, make sure you have the latest AMO libraries available before installing Tabular Editor. They can be downloaded from the [SQL Server 2016 Feature Pack](https://www.microsoft.com/en-us/download/details.aspx?id=52676) (Click "Download" and choose either "\X64\SQL_AS_AMO.msi" or "\X86\SQL_AS_AMO.msi").

## Introduction
View the article on www.kapacity.dk/tabular-editor for a general presentation of the tool and the motivations behind it.

Tabular Editor has a lot of features that makes it easier to work with Tabular Models. The recommended workflow is to set up the tables and relationships using SSDT as normal, and then use Tabular Editor to do the rest. That is: Create calculated columns, measures, hierarchies, perspectives, translations, display folders, and every other kind of fine-tuning you can think of.

Load a Model.bim file by choosing the Open > From File... option in the File menu (CTRL+O), or open an existing database from an instance of Analysis Services by choosing the Open > From DB... option. In the latter case, you will be prompted for a server name and optional credentials:

![Connecting to an already deployed Tabular Model](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/Connect.png)

*Note:* This also works with the new Azure Analysis Services PaaS.

After clicking "OK", you will be presented with a list of databases on the server.

This is how the UI looks after a model has been loaded into Tabular Editor:

![The main UI of Tabular Editor](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/Main%20UI.png)

The tree on the left side of the screen, displays all tables in the Tabular Model. Expanding a table will show all columns, measures and hierarchies within the table, grouped by their Display Folders. Use the buttons just above the tree, to toggle display folders, hidden objects, certain types of objects, or filter out objects by names. Right-clicking anywhere in the tree, will bring up a context menu with common actions, such as adding new measures, making an object hidden, duplicating objects, deleting objects, etc. Hit F2 to rename the currently selected object or multiselect and right-click to batch rename multiple objects.

![Batch Renaming lets you rename multiple objects simultaneously](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/BatchRename.png)

On the top right side of the main UI, you see the DAX Editor, which may be used to edit the DAX expression of any measure or calculated column in the model. Click the "DAX Formatter" button to automatically format the code through www.daxformatter.com.

Use the property grid in the lower right corner, to examine and set properties of objects, such as Format String, Description along with translations and perspective memberships. You can also set the Display Folder property here, but it's easier to simply drag and drop objects within the tree to update their Display Folder (try selecting multiple objects using CTRL or SHIFT).

To edit perspectives or translations (cultures), select the "Model" object in the tree, and locate the "Model Perspectives" or "Model Cultures" properties, in the property grid. Click the small elipsis button to open a collection editor for adding/removing/editing perspectives/cultures.

![Editing perspectives - click the elipsis button to the right](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/Edit%20Perspectives.png)

To save your changes back to the Model.bim file, click the save button or hit CTRL+S. If you opened an existing Tabular Database, the changes are saved directly back to the database. You will be prompted if the database was changed since you loaded it into Tabular Editor. You can always undo your changes by pressing CTRL+Z.

If you want to deploy your model to another location, go to the "Model" menu and choose "Deploy".

## Deployment
Tabular Editor comes with a deployment wizard that provides a few benefits compared to deploying from SSDT - especially when deploying to an existing database. After choosing a server and a database to deploy to, you have the following options for the deployment at hand:

![Deployment Wizard](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/Deployment.png)

Leaving the "Deploy Connections" box unchecked, will make sure that all the data sources on the target database stay untouched. You will get an error if your model contains one or more tables with a data source, that does not already exist in the target database.

Similarly, leaving out "Deploy Table Partitions", will make sure that existing partitions on your tables are not changed, leaving the data in the partitions intact.

When the "Deploy Roles" box is checked, the roles in the target database will be updated to reflect what you have in the loaded model, however if the "Deploy Role Members" is unchecked, the members of each role will be unchanged in the target database.

### Command Line usage
You can use the command line for automated deployment. All deployment options that are available through the GUI, are also available through the command line.

#### Deployment Examples

`TabularEditor.exe c:\Projects\Model.bim`

Opens the Tabular Editor GUI and loads the specified Model.bim file (without deploying anything).

`TabularEditor.exe c:\Projects\Model.bim -deploy localhost AdventureWorks`

Deploys the specified Model.bim file to the SSAS instance running on localhost, overwriting or creating the AdventureWorks database. The GUI will not be loaded.

By default, partitions, data sources and roles will not be overwritten in the target database. This behaviour can be changed by adding one or more of the following switches to the command above:

* `-P` Overwrite **p**artitions
* `-C` Overwrite **c**onnections (data sources)
* `-R` Overwrite **r**oles
* `-M` Overwrite role **m**embers

#### Note
Since TabularEditor.exe is a Windows Forms application, running it from the command line will execute the application in a different thread, returning control to the caller immediately. This may cause issues when running deployments as part of a batch job where you need to await succesful deployment before proceeding with the job. If you experience these issues, use `start /wait` to let TabularEditor finish its job before returning control to the caller:

`start /wait TabularEditor.exe c:\Projects\Model.bim -deploy localhost AdventureWorks`

## Advanced Scripting
Tabular Editor lets you use C# to script changes to the loaded model. This is practical when you want to apply several changes to many objects at once. The Advanced Script editor has access to two objects:

* `Selected` which represents all objects that are currently selected in the explorer tree.
* `Model` which represents the entire Tabular Object Model tree.

The Advanced Script editor has some limited IntelliSense functionality to get you started:

![IntelliSense helps you create scripts for Tabular Editor](https://raw.githubusercontent.com/otykier/TabularEditor/master/Documentation/AdvancedEditor%20intellisense.png)

More detailed documentation will be uploaded to GitHub soon.

# Version History

* **2016-09-01** Version 1.0 released on Kapacity.dk
* **2016-11-22** Version 2.0 and source code released on GitHub.com

# Roadmap

Below is a listing of features I'm currently working on for future releases:

* IntelliSense in DAX expression editor
* Formula fix-up (i.e. automatically fixing DAX expressions when renaming objects)
* UI for showing object dependencies
* Scripting changes from the command-line
* Possibility to read/edit more object types (tables, partitions, data columns, relationships, roles)
* Split a Model.bim into multiple json files (for example, one file per table) for better integration into Source Control workflows.
* Power BI compatibility