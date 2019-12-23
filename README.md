# EVO-ISO
Generates Locational Marginal Pricing information from data sources like PJM, NYISO, ISONE.

## PJM
Gets data from PJM's Rest API. Below are the market types
- Real-Time Hourly LMPs (rth)
- Real-Time Five Minute LMPs (rt5m)
- Day-Ahead Hourly LMPs (dah)
- Settlements Verified Hourly LMPs (svh)
- Settlements Verified Five Minute LMPs (sv5m)

## NYISO (New York Independent System Operator)
Gets data from archived NYISO website.
- Day-Ahead Market (DAM) LBMP Generator (dahg)
- Real-Time Market LBMP  Generator (rt5mg)
- Time-Weighted/Integrated Real-Time LBMP Generator (rttwhg)

## Pre-requisites
- Python 3
- Psql 9.x

## Install & Run

```sh
mkdir -p $HOME/repos/git/bitbucket/dynamo-ny 
cd $HOME/repos/git/bitbucket/dynamo-ny
git clone https://{user}@bitbucket.org/dynamo-ny/evods-iso.git
cd evods-iso/isobot 
pip install -r requirements.txt
scrapy crawl (pjm | nyiso) -a market_data_type=(market-types) -a start_date=(start-date) -a end_date=(end-date)
```

## Test

Validate the data in s3.
```sh
cd evods-iso/automation/scripts
python [validation_pjm.py | validation_nyiso.py] (start-date) (end-date) (market-type)
```

(or)

```sh
aws s3 ls --recursive s3://evoiso/iso/pjm/dah/ | awk '{print $4}' | cut -f4 -d/ | uniq | cut -c 1-6 | sort | uniq -c
```

Validate the data in Statuses table.
```sh
psql -U evoisodev -h evoiso-redshift.cscftq2dd2oa.us-west-2.redshift.amazonaws.com -p 5439 -d evoiso-development
evoiso-development=> SELECT DATE_PART(year, capture_date::date) AS year, DATE_PART(month, capture_date::date) AS month, COUNT(*) FROM statuses WHERE balancing_authority = 'PJM' AND market_data_type = 'dah' GROUP BY 1, 2 ORDER BY 1, 2;
```

## To-Do
- Capture data for ISONE for different market data types.

## Contributing

- Clone/fork it 
- Create your feature branch (`git checkout -b feature/fooBar`)
- Commit your changes (`git commit -am 'Add some fooBar'`)
- Push to the branch (`git push origin feature/fooBar`)
- Create a new Pull Request
