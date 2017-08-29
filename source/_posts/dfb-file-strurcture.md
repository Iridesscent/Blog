---
title: dbf file strurcture
id: 318
categories:
  - Database
date: 2016-11-17 17:09:32
tags:
---

&nbsp;

# <span style="color: teal; font-family: Arial,Helvetica,sans-serif;">Data File Header Structure for the
</span><span style="color: teal; font-family: Arial,Helvetica,sans-serif;">dBASE Version 7 Table File</span>

<span style="font-family: Arial,Helvetica,sans-serif;">
<span style="color: #000000;">**<span style="font-size: small;">Note:</span>**<span style="font-size: small;"> Unless prefaced by "0x", all numbers specified in the Description column of the following tables are decimal.</span></span></span>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1.1 Table File Header</span>

&nbsp;
<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER" height="19"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER" height="19"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td height="19"><center><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>** </span></center></td>
</tr>
<tr>
<td align="CENTER" height="115"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0</span></td>
<td align="CENTER" height="115"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="115"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Valid dBASE for Windows table file, bits 0-2 indicate version number: 3 for dBASE Level 5, 4 for dBASE Level 7.</span><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Bit 3 and bit 7 indicate presence of a dBASE IV or dBASE for Windows memo file; bits 4-6 indicate the presence of a dBASE IV SQL table; bit 7 indicates the presence of any .DBT memo file (either a dBASE III PLUS type or a dBASE IV or dBASE for Windows memo file).</span></td>
</tr>
<tr>
<td align="CENTER" height="65"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1-3</span></td>
<td align="CENTER" height="65"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">3 bytes</span></td>
<td height="65"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Date of last update; in YYMMDD format.  Each byte contains the number as a binary.  YY is added to a base of 1900 decimal to determine the actual year. Therefore, YY has possible values from 0x00-0xFF, which allows for a range from 1900-2155.</span></td>
</tr>
<tr>
<td align="CENTER" height="38"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4-7</span></td>
<td align="CENTER" height="38"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">32-bit number</span></td>
<td height="38"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of records in the table. (Least significant byte first.)</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8-9</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of bytes in the header. (Least significant byte first.)</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10-11</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of bytes in the record. (Least significant byte first.)</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">12-13</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2 bytes</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved; filled with zeros.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">14</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Flag indicating incomplete dBASE IV transaction.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">15</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">dBASE IV encryption flag.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-27</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">12 bytes</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved for multi-user processing.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">28</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Production MDX flag; 0x01 if a production .MDX file exists for this table; 0x00 if no .MDX file exists.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">29</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Language driver ID.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">30-31</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2 bytes</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved; filled with zeros.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">32-63</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">32 bytes</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Language driver name.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">64-67</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4 bytes</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved.</span></td>
</tr>
<tr>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">68-n</span></td>
<td align="CENTER" height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">48 bytes each</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field Descriptor Array (see 1.2).</span></td>
</tr>
<tr>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">n+1</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0x0D stored as the Field Descriptor terminator.</span></td>
</tr>
<tr>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">n+2</span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">See below for </span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">calculations of size</span></span></td>
<td height="19"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field Properties Structure</span></td>
</tr>
</tbody>
</table>
<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">n above is the last byte in the field descriptor array. The size of the array depends on the number of fields in the table file.</span>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1\. 2 Field Descriptor Array</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">(One for each field in the table)</span>
<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td><center><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>** </span></center></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0-31</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">32 bytes</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field name in ASCII (zero-filled).</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">32</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field type in ASCII (B, C, D, N, L, M, @, I, +, F, 0 or G).</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">33</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field length in binary.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">34</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field decimal count in binary.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">35-36</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2 bytes</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">37</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Production .MDX field flag; 0x01 if field has an index tag in the production .MDX file; 0x00 if the field is not indexed.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">38-39</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2 bytes</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">40-43</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4 bytes</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Next Autoincrement value, if the Field type is Autoincrement, 0x00 otherwise.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">44-47</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4 bytes</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved.</span></td>
</tr>
</tbody>
</table>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1.3 Field Properties Structure</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">This contains a header describing the Field Properties array, followed by the actual array, followed by property data. It is contained in the .DBF header and comes immediately after the Field Descriptor terminator (See Table 1.1).</span> <span style="font-family: Arial,Helvetica,sans-serif;">   </span>
<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td><center><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>** </span></center></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0-1</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of Standard Properties. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2-3</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Start of Standard Property Descriptor Array. (see 1.3.1 )</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4-5</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of Custom Properties. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">6-7</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Start of Custom Property Descriptor Array. (see 1.3.2 )</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8-9</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of Referential Integrity (RI) properties.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10-11</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Start of RI Property Descriptor Array. (see 1.3.3 ) </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">12-13</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Start of data - this points past the Descriptor arrays to data used by the arrays - for example Custom property names are stored here.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">14-15</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Actual size of structure, including data (Note: in the .DBF this will be padded with zeroes to the nearest 0x200, and may have 0x1A at the end). If the structure contains RI data, it will not be padded.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-n</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">15 bytes each</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Standard Property Descriptor Array (n = (15*number of standard properties) + 16). (see 1.3.1)</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">(n+1)-m</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">14 bytes each</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Custom Property Descriptor Array (m = n+ 14*number of custom properties). (see 1.3.2)</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">(m+1)-o</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">22 bytes each</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">RI Property Descriptor Array (o = m+ 22*number of RI properties). (see 1.3.3)</span></td>
</tr>
</tbody>
</table>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1.3.1 Standard Property and Constraint Descriptor Array</span>

<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td><center><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>** </span></center></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0-1</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Generational number. More than one value may exist for a property. The current value is the value with the highest generational number.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2-3</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Table field offset - base one. 01 for the first field in the table, 02 for the second field, etc. Note: this will be 0 in the case of a constraint.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Which property is described in this record:</span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">01 Required</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">02 Min</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">03 Max</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">04 Default </span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">06 Database constraint</span></span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">5</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field Type:</span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">00 No type - constraint</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">01 Char</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">02 Numeric</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">03 Memo</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">04 Logical </span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">05 Date</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">06 Float</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">08 OLE</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">09 Binary</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">11 Long</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">12 Timestamp</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">13 Double</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">14 AutoIncrement (not settable from the Inspector)</span></span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">6</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0x00 if the array element is a constraint, 0x02 otherwise.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">7-10</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">4 bytes</span></td>
<td><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">11-12</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset from the start of this structure to the data for the property. The Required property has no data associated with it, so it is always 0. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">13-14</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Width of database field associated with the property, and hence size of the data (includes 0 terminator in the case of a constraint).</span></td>
</tr>
</tbody>
</table>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1.3.2 Custom Property Descriptor Array</span>

<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>**</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0-1</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Generational number. More than one value may exist for a property. The current value is the value with the highest generational number. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">2-3</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Table field offset - base one.  01 for the first field in the table, 02 for the second field, etc.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Field Type </span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">01 Char</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">02 Numeric</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">03 Memo</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">04 Logical </span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">05 Date</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">06 Float</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">08 OLE</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">09 Binary</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">11 Long</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">12 Timestamp</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">13 Double</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">14 AutoIncrement (not settable from the Inspector)</span></span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">5</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Reserved</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">6-7</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset from the start of this structure to the Custom property name.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8-9</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Length of the Custom property name.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10-11</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset from the start of this structure to the Custom property data.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">12-13</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Length of the Custom property data (does not include null terminator).</span></td>
</tr>
</tbody>
</table>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">1.3.3 Referential Integrity Property Descriptor Array</span>

<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Byte</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Contents</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>**</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">0x07 if Master (parent), 0x08 if Dependent (child). </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1-2</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Sequential number, 1 based counting. If this number is 0, this RI rule has been dropped.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">3-4</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset of the RI rule name - 0 terminated.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">5-6</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Size of previous value.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">7-8</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset of the name of the Foreign Table - 0 terminated.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">9-10</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Size of previous value.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">11</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Update &amp; delete behaviour:</span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">Update Cascade 0x10</span></span> <span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">
<span style="color: #000000;">Delete Cascade 0x01</span></span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">12-13</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number of fields in the linking key.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">14-15</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset of the Local Table tag name - 0 terminated.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-17</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Size of previous value.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">18-19</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Offset of the Foreign Table tag name - 0 terminated.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">20-21</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">16-bit number</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Size of previous value.</span></td>
</tr>
</tbody>
</table>
<span style="color: #000000; font-family: Arial,Helvetica,sans-serif;"><span style="font-size: small;">(Foreign = in the other table,  Local = in this table)</span></span>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">Property Data</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">For standard properties, everything is stored exactly as it is in the Table records. Custom property data is stored as the Name string, followed immediately by the Value string, and a null terminator. The Constraint text is stored as a null-terminated string.</span>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">Table Records</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">The records follow the header in the table file. Data records are preceded by one byte, that is, a space (0x20) if the record is not deleted, an asterisk (0x2A) if the record is deleted. Fields are packed into records without field separators or record terminators. The end of the file is marked by a single byte, with the end-of-file marker, an OEM code page character value of 26 (0x1A).</span>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">Storage of dBASE Data Types</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Except for autoincrement fields, all types are initialized to binary zeroes. In addition, any fields which have been assigned a default property will contain the default value.</span>
<table border="1"><caption> </caption>
<tbody>
<tr>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Symbol</span>**</span></td>
<td align="CENTER"><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Data Type</span>**</span></td>
<td><span style="font-family: Arial,Helvetica,sans-serif; font-size: small;">**<span style="color: #000000;">Description</span>**</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">B</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Binary, a string</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10 digits representing a .DBT block number. The number is stored as a string, right justified and padded with blanks.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">C</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Character</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">All OEM code page characters - padded with blanks to the width of the field.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">D</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Date</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8 bytes - date stored as a string in the format YYYYMMDD.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">N</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Numeric</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number stored as a string, right justified, and padded with blanks to the width of the field. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">L</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Logical</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">1 byte - initialized to 0x20 (space) otherwise T or F.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">M</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Memo, a string</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10 digits (bytes) representing a .DBT block number. The number is stored as a string, right justified and padded with blanks.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">@</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Timestamp</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8 bytes - two longs, first for date, second for time.  The date is the number of days since  01/01/4713 BC. Time is hours * 3600000L + minutes * 60000L + Seconds * 1000L</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">I</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Long</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">4 bytes. Leftmost bit used to indicate sign, 0 negative.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">+</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Autoincrement</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Same as a Long</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">F</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Float</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Number stored as a string, right justified, and padded with blanks to the width of the field. </span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">O</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Double</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">8 bytes - no conversions, stored as a double.</span></td>
</tr>
<tr>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">G</span></td>
<td align="CENTER"><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">OLE</span></td>
<td><span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">10 digits (bytes) representing a .DBT block number. The number is stored as a string, right justified and padded with blanks.</span></td>
</tr>
</tbody>
</table>

### <span style="color: #000000; font-family: Arial,Helvetica,sans-serif;">Binary, Memo, OLE Fields and .DBT Files</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Binary, memo, and OLE fields store data in .DBT files consisting of blocks numbered sequentially (0, 1, 2, etc.). SET BLOCKSIZE determines the size of each block. The first block in the .DBT file, block 0, is the .DBT file header.</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">Each binary, memo, or OLE field of each record in the .DBF file contains the number of the block (in OEM code page values) where the field's data actually begins. If a field contains no data, the .DBF file contains blanks (0x20) rather than a number.</span>

<span style="color: #000000; font-family: Arial,Helvetica,sans-serif; font-size: small;">When data is changed in a field, the block numbers may also change and the number in the .DBF may be changed to reflect the new location.</span>