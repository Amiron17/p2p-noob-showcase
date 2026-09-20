# Test Plan

## Objective

Verify that the current P2P NOOB build performs its main monitoring and historical-data functions correctly, handles common invalid inputs and external failures safely, and remains stable during continuous operation.

## Scope

Testing covered:

- web interface startup and monitoring flow;
- user input validation;
- Bybit P2P request/response handling;
- merchant-quality and user-compatibility filtering;
- best-order selection;
- USD/RUB reference usage;
- edge calculation;
- Telegram notifications;
- continuous live monitoring;
- historical market collection;
- retry and timeout handling;
- PostgreSQL persistence;
- long-running stability;
- retest and regression around selected fixes.

## Test approach

The QA iteration included:

- smoke testing;
- functional testing;
- negative testing;
- exploratory testing;
- endurance testing;
- research testing;
- retest;
- targeted regression.

## Environment

- Windows
- Python virtual environment
- Flask web application
- local PostgreSQL database
- Bybit P2P external endpoint
- cached USD/RUB reference
- Telegram bot integration
- local browser

## Exit criteria

The QA cycle could be closed when:

- the critical user flow had been exercised;
- major negative/error scenarios had been tested;
- confirmed defects were documented;
- selected fixes were retested;
- nearby behavior around the fixes remained functional;
- endurance results had been reviewed;
- no known blocker prevented the main demo flow.
