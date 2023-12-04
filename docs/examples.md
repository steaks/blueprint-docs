# Examples 

## Hello World

This example is a simple application that demonstrates the basics of Blueprint. Browse the code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld). Or run the application locally by following these instructions:

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh -t helloWorld MyHelloWorldApplication
cd MyHelloWorldApplication
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```

## User Profile

This example is an application with that allows a user to view and edit their email, first name, and last name on a user profile page. Browse the code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld). Or run the application locally by following these instructions:

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh -t userProfile MyUserProfileApplication
cd MyUserProfileApplication
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```