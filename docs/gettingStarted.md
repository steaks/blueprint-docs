# Getting Started

## Prerequisites

Install [Node](https://nodejs.org/en/) with versions node >= 16 and npm >= 8. Install [GNU make](https://www.gnu.org/s/make/manual/make.html) version >= 3.

## Initialize the project

Download the createBlueprint script and execute the script to initialize a new project.

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh {your-app-name}
```

## Install, build, and run

```shell
cd {your-app-name}
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```