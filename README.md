# EVO-datascience
> Generates Locational Marginal Pricing information from data sources like PJM, NYISO, ISONE.
> Data is generated for the following market types:

### PJM : Gets data from PJM Rest API.
> Real-Time Hourly LMPs
> Real-Time Five Minute LMPs
> Day-Ahead Hourly LMPs
> Settlements Verified Hourly LMPs
> Settlements Verified Five Minute LMPs

### NYISO : Gets data from archieved NYISO website.
> Day-Ahead Market (DAM) LBMP Generator
> Real-Time Market LBMP  Generator
> Time-Weighted/Integrated Real-Time LBMP Generator

## Run command

- To generate the data, run the command from "\evods-iso\isobot" path:

_scrapy crawl [pjm] -a market_data_type=[dah] -a start_date=[2019-12-01] -a end_date=[2019-12-01]_

- To validate the data in s3, run the command from "evods-iso\automation\scripts" path:

_python [validation_pjm.py] 2019-12-01 2019-12-01 dah_

		(or)
_aws s3 ls --recursive s3://evoiso/iso/pjm/dah/ | awk '{print $4}' | cut -f4 -d/ | uniq | cut -c 1-6 | sort | uniq -c_

- To validate the data in Statuses table, run the command:

_SELECT DATE_PART(year, capture_date::date) AS year, DATE_PART(month, capture_date::date) AS month, COUNT(*) FROM statuses WHERE balancing_authority = 'PJM' AND market_data_type = 'dah' GROUP BY 1, 2 ORDER BY 1, 2;_

## Development setup

The following should be installed:

```sh
boto3
scrapy
sqlalchemy
logging
psycopg2
tqdm
requests
bs4
zipfile
```

## Release History

* {version1}
    * Work in progress

## Installation

Bitbucket:

```sh
git clone https://{user}@bitbucket.org/dynamo-ny/evods-iso.git
```

## Contributing

1. Clone/fork it 
2. Create your feature branch (`git checkout -b feature/fooBar`)
3. Commit your changes (`git commit -am 'Add some fooBar'`)
4. Push to the branch (`git push origin feature/fooBar`)
5. Create a new Pull Request

## Testing
> Check for the data in Statuses table.
> Check for the data in aws s3.
