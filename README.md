# **Windows Forensic Investigation Using Registry Analysis**

In this project, I demonstrated how I used registry analysis to investigate potential unauthorized access and suspicious activities on a Windows system. The Windows Registry stored critical configuration data about the system, its users, and the software it ran, making it a valuable resource for forensic investigators. By analyzing registry artifacts, I was able to trace user actions, connected devices, and software executions.

To conduct this investigation, I utilized tools such as **Registry Explorer** and **Eric Zimmerman’s EZtools**, which helped extract and analyze registry data efficiently. I explored artifacts like user accounts, USB connections, and system logs to uncover key information relevant to the investigation scenario. The goal was to analyze the evidence from a machine suspected of unauthorized access and produce detailed forensic findings.

For this challenge, I began by launching the **RegistryExplorer** from the “EZtools” folder on the desktop of the provided VM. Running the program as an administrator allowed me to load the necessary hives for this investigation. I focused on loading the **SAM**, **SOFTWARE**, and **SYSTEM** hives located in the `C:\Windows\System32\Config` directory.

<img width="805" alt="Registry Explorer Loading Hives" src="https://github.com/user-attachments/assets/9d69d279-b5d6-4608-883f-01e314069e67">

<img width="787" alt="Hives Loaded in Registry Explorer" src="https://github.com/user-attachments/assets/cd9c5e67-62da-491d-a7e2-2bccc90993b0">

### **Registry Forensics**:
In forensic investigations, it is crucial to preserve the exact state of the data as it was found. By opting not to replay the transaction logs (choosing “No”), I ensured that the hive remains unchanged from the moment it was captured.

**Investigative Relevance**: Loading a dirty hive can still reveal valuable information. Even though the hive was not cleanly closed, I can recover and analyze the data without applying further changes that might interfere with the forensic integrity of the evidence.

<img width="804" alt="Hive Loaded Dirty" src="https://github.com/user-attachments/assets/a27ac6c0-a050-4e9a-8d35-45171e937cf9">

<img width="806" alt="Investigating Hive Data" src="https://github.com/user-attachments/assets/10b78a69-1112-4bc4-8cb2-6a2d89a60955">

### **User Accounts Investigation**:

The first few challenge questions pertained to user accounts, so I analyzed the **SAM** hive. The **SAM** (Security Accounts Manager) file in the Windows Registry manages user account information, including usernames and password hashes.

<img width="802" alt="Analyzing SAM Hive" src="https://github.com/user-attachments/assets/27a97e3f-72e5-4138-9baa-e016e86a08b4">

During the analysis of the system, I identified a total of **six user accounts** present on the machine. Out of these, **three accounts were user-created**, while the remaining **three were system-generated accounts**.

- **Three system-generated accounts:**
  - **Administrator (User ID: 500)**: Built-in account for administering the computer/domain.
  - **Guest (User ID: 501)**: Built-in account for guest access to the computer/domain.
  - **WDAGUtilityAccount (User ID: 504)**: A system account used by Windows Defender Application Guard.
  
<img width="1064" alt="System-Generated Accounts" src="https://github.com/user-attachments/assets/12761545-12a3-4f94-a750-626ccc65883e">

<img width="1062" alt="User Accounts Details" src="https://github.com/user-attachments/assets/076597d3-f5e0-48d0-807e-8df385bbc7ac">

- **Three user-created accounts:**
  - **THM-4n6 (User ID: 1001)**: This account was a member of the Administrators group. A password hint “count” was set for this account.
  - **thm-user (User ID: 1002)**: Belonged to the Users group and had no password hint set.
  - **thm-user2 (User ID: 1003)**: Also part of the Users group, this account had never logged into the system.
  
<img width="1150" alt="User-Created Accounts" src="https://github.com/user-attachments/assets/64534060-b715-4d18-a774-90776fdec224">

<img width="1093" alt="Detailed User Information" src="https://github.com/user-attachments/assets/b85d2d42-d4ed-47c2-b8de-c614bb9b4f0c">

### **Investigating Recent File Access**:

As part of the forensic investigation, I delved into the **NTUSER.DAT** file to gather more information about user activity on the system. The **NTUSER.DAT** file is a critical registry hive that stores user-specific configuration and preferences for Windows systems. Every user on a Windows machine has their own **NTUSER.DAT** file, which is loaded into the system when the user logs in and remains active throughout their session.

This file contains a wealth of information about user behavior, including:

- **Recently opened files and applications**
- **User-specific software settings**
- **Recently accessed network resources**

After loading the **NTUSER.DAT** hive into **Registry Explorer**, I navigated to `Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` to investigate the recent file access. The file **‘Changelog.txt’** was accessed on **2021-11-24 18:18:48**.

<img width="1468" alt="Recent File Access: Changelog.txt" src="https://github.com/user-attachments/assets/c3216fb6-cf78-4e39-a225-b1781942191e">


### **Searching for Program Executions: NTUSER.DAT Investigation**

Given the importance of understanding user activity during the investigation, I specifically searched the **NTUSER.DAT** hive for evidence of program executions. By searching for terms like **‘Apps’**, I was able to locate the **RecentApps** section, which logs details about recently executed applications by the user. This provided me with crucial insights into the programs the user had run, including timestamps and execution paths.

During this search, I identified the execution of the **Python 3.8.2 installer**. The registry revealed that the installer was run from the following path:

- **Z:\setups\python-3.8.2.exe**

This information is significant because it gives insight into the software installed by the user and may help correlate activity on the system with specific actions or events. The execution path provides a detailed look into where the software was stored and potentially highlights external storage or network drives that may have been involved.

<img width="1468" alt="Program Execution: Python 3.8.2 Installer" src="https://github.com/user-attachments/assets/b41a56da-5aae-43b2-bb17-85520ab4c497">


### **Investigating USB Device Connections**:

To investigate USB devices, I explored both the **SYSTEM** and **SOFTWARE** hives, particularly focusing on the `SOFTWARE\Microsoft\Windows Portable Devices\Devices` directory to locate the GUID of the device with the friendly name “USB.” I then switched to `SYSTEM\CurrentControlSet\Enum\USBSTOR` to extract more detailed information about the device, including the last connection timestamp.

The USB device with the friendly name **‘USB’** was last connected on **2021-11-24 18:40:06**.

<img width="1469" alt="USB Device Connection Details" src="https://github.com/user-attachments/assets/f8d75f4e-8109-4d53-8669-ce2c56534bae">


<img width="1467" alt="Detailed USB Information" src="https://github.com/user-attachments/assets/549224a2-2527-4160-b043-e2538ffed5eb">


During the investigation, I utilized a **Windows Forensics Cheatsheet** to streamline my process of locating key forensic artifacts within the Windows Registry hives. The cheatsheet, which covers essential registry paths for gathering system information, user activity, and program execution evidence, significantly aided in navigating complex structures like **NTUSER.DAT** and **SYSTEM**. By using this resource, I efficiently found relevant data, such as executed programs and recent user activities, expediting the investigative process and ensuring thorough coverage of critical evidence.

### **Summary:**

In this forensic investigation, I analyzed the Windows Registry to identify user accounts, recent file access, program executions, and connected USB devices. By leveraging tools like **Registry Explorer** and exploring key registry hives such as **SAM**, **SOFTWARE**, **SYSTEM**, and **NTUSER.DAT**, I successfully uncovered evidence of unauthorized access and activity on the system.


## Contributing

We welcome contributions from the community. If you have any suggestions, bug reports, or feature requests, please open an issue or submit a pull request. 

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact

For any inquiries or further information, please contact me at [martaa.dominik@gmail.com](mailto:martaa.dominik@gmail.com).

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/marta-dominik-a67803233/)
