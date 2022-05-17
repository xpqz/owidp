# OWID Covid data

This repository contains a subset of Our World In Data's Covid-19 data. The raw csv data can be found [here](https://covid.ourworldindata.org/data/owid-covid-data.csv). We imported this into a sqlite3 database by 

    % sqlite3 owid
    sqlite> .mode csv
    sqlite> .import owid-covid-data.csv owid
    
We then built a subset containing only data from United States, Canada, United Kingdom, France, Denmark, picking two fields (new_cases_per_million and new_deaths_per_million) and converting the dates to epoch seconds:

    sqlite> create table subset (location text, date int, cases int, deaths int);
    sqlite> insert into subset select location, unixepoch(date), cast(new_cases_per_million as integer), cast(new_deaths_per_million as integer) from owid where location in ('United States', 'Canada', 'United Kingdom', 'France', 'Denmark')

Finally, we exported this data from the 1st of Jan, 2022:

    sqlite> .mode csv
    sqlite> .output covid_subset2.csv
    sqlite> select * from subset where date>=1640995200;
    sqlite> .output stdout
