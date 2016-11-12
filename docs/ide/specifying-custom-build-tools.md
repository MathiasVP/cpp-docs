---
title: "Specifying Custom Build Tools | Microsoft Docs"
ms.custom: ""
ms.date: "11/04/2016"
ms.prod: "visual-studio-dev14"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "devlang-cpp"
ms.tgt_pltfrm: ""
ms.topic: "article"
f1_keywords: 
  - "VC.Project.VCCustomBuildTool.CustomBuildToolBeforeTargets"
  - "VC.Project.VCCustomBuildTool.Outputs"
  - "VC.Project.VCCustomBuildTool.Command"
  - "VC.Project.VCCustomBuildTool.CommandLine"
  - "VC.Project.VCCustomBuildTool.AdditionalDependencies"
  - "VC.Project.VCCustomBuildTool.Message"
  - "VC.Project.VCCustomBuildTool.CustomBuildToolAfterTargets"
  - "VC.Project.VCCustomBuildTool.Description"
  - "VC.Project.VCCustomBuildTool.AdditionalInputs"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "build tools (C++), specifying"
  - "custom build tools (C++), specifying"
  - "builds (C++), custom build tools"
ms.assetid: e5161946-8002-418d-991e-219e6a8586f5
caps.latest.revision: 11
author: "corob-msft"
ms.author: "corob"
manager: "ghogen"
translation.priority.ht: 
  - "cs-cz"
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pl-pl"
  - "pt-br"
  - "ru-ru"
  - "tr-tr"
  - "zh-cn"
  - "zh-tw"
---
# Specifying Custom Build Tools
A *custom build tool* provides the build system with the information it needs to build specific input files. A custom build tool specifies a command to run, a list of input files, a list of output files that are generated by the command, and an optional description of the tool.  
  
 For general information about custom build tools and custom build steps, see [Understanding Custom Build Steps and Build Events](../ide/understanding-custom-build-steps-and-build-events.md).  
  
### To specify a custom build tool  
  
1.  Open the project's **Property Pages** dialog box. For more information, see [Setting Visual C++ Project Properties](../ide/working-with-project-properties.md).  
  
2.  Click **Configuration Properties** to enable the **Configuration** box. In the **Configuration** box, select the configuration for which you want to specify a custom build tool.  
  
3.  In **Solution Explorer**, select the input file for the custom build tool.  
  
     If the **Custom Build Tool** folder does not appear, the file extension of the file you selected is associated with a default tool. For example, the default tool for .c and .cpp files is the compiler. To override a default tool setting, in the **Configuration Properties** node, in the **General** folder, in the **Item Type** property, click **Custom Build Tool**. Click **Apply** and the **Custom Build Tool** node is displayed.  
  
4.  In the **Custom Build Tool** node, in the **General** folder, specify the properties associated with the custom build tool:  
  
    -   In **Additional Dependencies**, specify any additional files beyond the one for which the custom build tool is being defined (the file associated with the custom build tool is implicitly considered an input to the tool). Having additional input files is not a requirement for a custom build tool. If you have more than one additional input, separate them with semicolons.  
  
         If an **Additional Dependencies** file's date is later than the input file, then the custom build tool is run. If all of the **Additional Dependencies** files are older than the input file, and the input file is older than the **Outputs** property file, then the custom build tool is not run.  
  
         For example, suppose you have a custom build tool that takes MyInput.x as input and generates MyInput.cpp, and that MyInput.x includes a header file, MyHeader.h. You can specify MyHeader.h as an input dependency to MyInput.x, and the build system will build MyInput.cpp when it is out-of-date with respect to MyInput.x or MyHeader.h.  
  
         Input dependencies can also ensure that your custom build tools run in the order you need them to. In the preceding example, suppose that MyHeader.h is actually the output of a custom build tool. Because MyHeader.h is a dependency of MyInput.x, the build system will first build Myheader.h before running the custom build tool on MyInput.x.  
  
    -   In **Command Line**, specify a command as if you were specifying it at the command prompt. Specify a valid command or batch file, and any required input or output files. Specify the **call** batch command before the name of a batch file to guarantee that all subsequent commands are executed.  
  
         Multiple input and output files can be specified symbolically with MSBuild macros. [!INCLUDE[crabout](../build/reference/includes/crabout_md.md)] specifying the location of files, or the names of sets of files, see [Common Macros for Build Commands and Properties](../ide/common-macros-for-build-commands-and-properties.md).  
  
         Because the '%' character is reserved by MSBuild, if you specify an environment variable replace each **%** escape character with the **%25** hexadecimal escape sequence. For example, replace **%WINDIR%** with **%25WINDIR%25**. MSBuild replaces each **%25** sequence with the **%** character before it accesses the environment variable.  
  
    -   In **Description**, type a descriptive message about this custom build tool. The message is printed to the **Output** window when the build system processes this tool.  
  
    -   In **Outputs**, specify the name of the output file. This is a required entry; without a value for this property, the custom build tool will not run. If a custom build tool has more than one output, separate file names with a semicolon.  
  
         The name of the output file should be the same as it is specified in the **Command Line** property. The project build system will look for the file and check its date. If the output file is newer than the input file or if the output file is not found, the custom build tool is run. If all of the **Additional Dependencies** files are older than the input file, and the input file is older than the file specified in the **Outputs** property, the custom build tool is not run.  
  
 If you want the build system to operate on an output file generated by the custom build tool, you must manually add it to the project. The custom build tool will update the file during the build.  
  
## Example  
 Assume that you want to include in your project a file that is named parser.l. You want a lexical analyzer to process parser.l to produce a .c file that has the same base name (parser.c).  
  
 First, you add parser.l and parser.c to the project. If the files do not yet exist, you just add a reference to the files. You create a custom build tool for parser.l and type the following in the **Commands** property:  
  
```  
lexer %(FullPath) .\%(Filename).c  
```  
  
 This command will run the lexical analyzer on parser.l and output parser.c to the project directory.  
  
 In the **Outputs** property, type the following:  
  
```  
.\%(Filename).c  
```  
  
 When you build the project, the build system compares the timestamps of parser.l and parser.c. If parser.l is more recent, or if parser.c doesn't exist, the build system runs the value of the **Command Line** property to bring parser.c up to date. Since parser.c was also added to the project, the build system then compiles parser.c.  
  
## See Also  
 [Common Macros for Build Commands and Properties](../ide/common-macros-for-build-commands-and-properties.md)   
 [Troubleshooting Build Customizations](../ide/troubleshooting-build-customizations.md)