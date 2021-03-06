# nem-reader

[![PyPI version](https://badge.fury.io/py/nemreader.svg)](https://badge.fury.io/py/nemreader) [![Build Status](https://travis-ci.org/aguinane/nem-reader.svg?branch=master)](https://travis-ci.org/aguinane/nem-reader) [![Coverage Status](https://coveralls.io/repos/github/aguinane/nem-reader/badge.svg)](https://coveralls.io/github/aguinane/nem-reader)

The Australian Energy Market Operator (AEMO) defines a [Meter Data File Format (MDFF)](https://www.aemo.com.au/Stakeholder-Consultation/Consultations/Meter-Data-File-Format-Specification-NEM12-and-NEM13) for reading energy billing data. 
This library sets out to parse these NEM12 (interval metering data) and NEM13 (accumulated metering data) data files into a useful python object, for use in other projects.

## Usage

First, read in the NEM file:
```python
from nemreader import read_nem_file
m = read_nem_file('examples/unzipped/Example_NEM12_actual_interval.csv')
```

You can see what data for the NMI and suffix (channel) is available:
```python
> print(m.header)
HeaderRecord(version_header='NEM12', creation_date=datetime.datetime(2004, 4, 20, 13, 0), from_participant='MDA1', to_participant='Ret1')

> print(m.transactions)
{'VABD000163': {'E1': [], 'Q1': []}}
```

Standard suffix/channels are defined in the [National Metering Identifier Procedure](https://www.aemo.com.au/-/media/Files/Electricity/NEM/Retail_and_Metering/Metering-Procedures/2018/MSATS-National-Metering-Identifier-Procedure.pdf).
`E1` is the general consumption channel (`11` for NEM13). 

Most importantly, you will want to get the energy data itself:

```python
> for nmi in m.readings:
>     for channel in m.readings[nmi]:
>         for reading in m.readings[nmi][suffix][-1:]:
>             print(reading)
Reading(t_start=datetime.datetime(2004, 4, 17, 23, 30), t_end=datetime.datetime(2004, 4, 18, 0, 0), read_value=14.733, uom='kWh', quality_method='S14', event='', read_start=None, read_end=None)
```

## Transpose to CSV

You can also output the NEM file in a more human readable format:

```python
from nemreader import output_as_csv
file_name = 'examples/unzipped/Example_NEM12_actual_interval.csv'
output_file = output_as_csv(file_name)
```

Which outputs transposed values to a csv file for all channels:

```
period_start,period_end,E1,Q1,quality_method
2004-02-01 00:00:00,2004-02-01 00:30:00,1.111,2.222,A
2004-02-01 00:30:00,2004-02-01 01:00:00,1.111,2.222,A
...
```
