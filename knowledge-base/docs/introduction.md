# Introduction

## Landmark Pattern Language (LPL) for Configurations

### What Is the Configuration Console?

The Configuration Console is a tool in the Infor Rich Client that enables administrators to make several types of changes that affect Landmark applications and users.

#### Application

Look-and-feel changes to menus, pages, and various user interface objects and actions associated with a business class, and configuration changes for features that apply across a data area, including:

- Enabling and disabling data translation (multi-language field configuration)
- Creating business subjects
- Creating and modifying MIME types

#### Security

Creation and modification of security classes. You can also use the Configuration Console for the maintenance of actor, identity, and role records.

#### Web Services

Creation of new web services. Web services enable non-Landmark systems to view or update Landmark data.

When you make configuration changes through the Configuration Console, the changes apply across the application and to all users.

## Configuration Console Development Process and the Landmark Pattern Language

The Landmark Pattern Language (LPL) is the language that many of the source files in Landmark applications are written in, such as those for business classes and user interface objects. The Configuration Console user interface allows manipulation of an application's LPL. 

### How Configurations Work

The application LPL source file integrity is fully protected by the Configuration Console:

- The LPL source file is **not modified** by the console
- A copy of the LPL source file is created and stored to a database table as a configuration
- At application execution time, this configuration is brought into memory as an overlay of the base source file
- The configuration is executed in place of the original LPL source file

**Key Point:** Configurations are dynamic and take effect in real time. Please use caution if using the Configuration Console on a production instance of an application.

### Best Practices

When creating configurations with the Configuration Console, it is best to do so in a provisional or test scenario before making the configurations available to all users. If you make the configuration changes directly to a production environment, the changes are applied as soon as you save them in the Configuration Console.

### Working with LPL

Generally, you should use the Configuration Console user interface to add, delete, and modify configurations. However, it can be useful to view the LPL in the Configuration Console to better understand possible configuration changes. For example, you might want to view the LPL for your configuration and then compare it to the original LPL.

The LPL is exposed through **Edit LPL** buttons and **view base LPL** links. If you view LPL in the Configuration Console, you can use `Ctrl+F` to open a search box at the bottom of the LPL pane to search through the LPL text.

#### Manual LPL Editing

Although rare, it might be useful to manipulate a configuration's LPL manually using the Edit LPL pane. The remainder of this document serves as a reference guide for understanding the LPL.

> **IMPORTANT:** Manually modifying the LPL is at your own risk. These manual modifications are not supported by Infor.

## Configuration Verification Tools

You have a selection of tools to help you identify configuration issues. These are in addition to any error messages that you see while working on a configuration in the Configuration Console. These tools can help you identify invalid configurations, locate specific errors within configurations, and compare different versions of a configuration to better understand what might be causing a problem.

### Verify Configurations (cdverify) Utility

Use the utility to quickly list invalid configurations and show any syntax errors in configurations.

### Configuration Console Tools

The Configuration Console includes tools for:
- Comparing configurations
- Locating invalid syntax
- Identifying invalid configurations

### Configuration Business Class Forms

The Data menu in the Infor Rich Client lets you:
- View a list of configurations of a single type
- Identify invalid ones of that type
- Compare any two versions of the configuration

## Additional Resources

Please refer to the Landmark documentation **Infor Landmark Technology User Interface Guides** for detailed information on using the Configuration Console.
