# Algorand.

launching a Beaker-powered betting dApp
   This tutorial will help you launch an AlgoBet instance, a betting dApp created using the Beaker framework.

repository for AlgoBet on github.

Requirements
general familiarity with Python 3.10+ and Algorand Smart Contracts (installation instructions are provided in the tutorial)
Beaker package (instructions for installation are included in the lesson)
Sandbox for Algorand (installation instructions are included in the lesson)

Background Tutorial Advancement
Setting up a suitable environment that includes a functional installation of Beaker and the Algorand sandbox connected to the testnet will be the first step.
It will be essential to make a wallet, add at least two accounts to it, and then fund those accounts with any of the testnet faucets that are accessible.
The AlgoBet smart contract will then be cloned from its repository, converted into TEAL code, and deployed on the testnet as the following stage.
In order to place a wager, determine the outcome, and receive the payment, we will next communicate with the deployed dApp.

Smart contract aims
Users can wager on the outcome of a certain event using AlgoBet, a simple decentralized betting system based on the Algorand smart contract. The traditional sport wagers, like those on basketball or football, served as the basis for the betting system. The average wager in these sports can cover one of three game outcomes: Home team wins, Away team wins, or Draw.
It will be necessary to deploy many AlgoBet instances in order to manage various events because each instance of AlgoBet will manage a specific event.

The stake for the wager is set at 140 milliAlgos. Users are not permitted to wager more than this fixed stake or more than one wager per user. A time management system is used to handle events and the associated bets.

to specify a payout time window and the event start and finish times as two timestamps. Additionally, until the winning wagers are paid, this technique prevents the management from deleting the contract.

The AlgoBet workflow involves three actors:

Manager: The manager controls the creation and deletion of smart contract instances as well as the setting of the oracle account;
Oracle: After an event has finished, its result can be changed by the Oracle account. In theory, it serves as the connection between the smart contract and the outside world; in practice, it is an Algorand account that the management selected when the contract was created;
Users: Users can interact with the smart contract by betting on the event and then reclaiming their winnings to specify a payout time window and the event start and finish times as two timestamps. Additionally, until the winning wagers are paid, this technique prevents the management from deleting the contract.

 on the accurate outcome. To access these features, they must opt-in to the contract.
Beaker, a cutting-edge development framework that makes it simpler to construct PyTeal code, was used to create AlgoBet, which was then tested on the Algorand sandbox.

Oracles
A betting service must retrieve the essential data about the pertinent event in order to function successfully. Oracles, or entities (person, software, sensor,a smart contract with components (sensor, etc.) that connect the off-chain and on-chain ecosystems.
For the purpose of obtaining the event result, AlgoBet must communicate with an oracle. You will build an Agorand account in this tutorial that will act as an Oracle account.

A brand-new programming framework for creating smart contracts on Algorand is called Beaker. The conventional PyTeal framework has proven to be challenging for some since it might end up being new, confusing, and having a structure that defies logic.
The purpose of Beaker is to enhance the development experience for both seasoned and novice algebra developers.
Few attempts were made to leverage Beaker for DApps implementations due to its recent availability. It requires such an activity to evaluate its potential and determine what has to be improved.


Steps
1. The environment
2. Establishing a wallet and accounts
3. Funding the account
6. Specify the occasion
7. Launch AlgoBet as Manager.
8. As Participant 1, place a wager.
(Optional) 9. Examine the application's state
10. Examine Participant 1's Account State (optional)
11. Place a wager as Player 2.
12. Select Oracle as the outcome.
13. Request payment on behalf of Participant 1 (the winner)
14. Request the payout as Participant 2 (looser) (optional).
15, optional Get rid of the AlgoBet dApp




1. The environment

You can use the instructions in this section to build up a suitable environment so that you can deploy and use an AlgoBet smart contract instance on your own. Ubuntu was used to test this configuration.

Please first make a new folder with your preferred name and put everything you'll need for following this instruction in it. We'll refer to that folder moving forward as the root directory.

Installing the following dependencies is necessary in order to run the smart contract.

Python: Because the Beaker framework requires Python 3.10 or later, we advise installing it. Most of the time, your operating system comes with a Python installation already installed. Open a terminal and type python3 --version to find out what version of Python you are using. Please consult the Python documentation to install a more recent version if your installation of Python is less than 3.10.
Creating a virtual environment (venv) is advised before continuing with this tutorial. Open a terminal, navigate to the root directory, and type:


# On Ubuntu or MacOS
python3 -m venv venv
source ./venv/bin/activate

# On Windows
python3 -m venv venv
.\venv\Scripts\activate

With this, you may construct a virtual environment. If the (venv) word appears at the beginning of your command line, the virtual environment will be successfully launched.

Beaker is a Python framework for creating Smart Contracts using PyTeal on Algorand. It is this framework that has been used to create the AlgoBet smart contract.

Beaker 0.3.6 has been the foundation for this tutorial's development. Pip can be used to install it:


pip install beaker-pyteal==0.3.6

Docker and Docker Compose: To run the Algorand sandbox, a functional Docker installation is required. We advise using the Docker documentation to install Docker Desktop if you are new to Docker.

Algorand Sandbox: Using Docker, this tool makes it simple and fast to set up, configure, and deploy an Algorand node. You can administer the containerized node through the sandbox environment and it can be connected to any of the three Algorand networks.

Move into the root directory and then clone the sandbox repository to install the Algorand sandbox:

git clone https://github.com/algorand/sandbox.git

Make sure Docker is running, then carry out the following commands to spin up the sandbox and attach it to the Algorand testnet:

cd sandbox
./sandbox up testnet

Your environment now meets all the criteria, so we can get started on the main portion of this tutorial!

2. Establishing a wallet and accounts
We will choose and use a few accounts from the sandbox to deploy and interact with AlgoBet. It will be necessary to manually establish a new wallet, fill it with new accounts, and fund them using a testnet faucet in order to use the sandbox in testnet mode.


To do that, use the terminal that is currently running the sandbox. Run the following instructions:


/sandbox enter algod
goal wallet new <walletName>

You must first log in to the node before you can begin creating the wallet by supplying the wallet name.
The command execution will inquire for the wallet's password and other security options.
When it's finished, you may verify that the wallet was successfully created by executing:

goal wallet list

And look for the wallet you just made.
If everything is in order, you can continue by adding some accounts to the wallet. While remaining in the algod node, you can perform:

goal account new -w <walletName>

We must set up 4 accounts.
By typing: you can determine whether the account was created correctly.

goal account list

After checking everything, you can finally leave the algod node:

Exit

And following this will take you to the common terminal.

3. Funding the account
Use this testnet dispenser to add some virtual money to your test accounts.

These steps can be used to fund each account:

finish solving the Captcha puzzle
the target address textbox after pasting the account address there, then clicking "Dispense"
By using the goal account list command mentioned in the preceding step, you can check your account balances at any moment.

4. Clone storage
After establishing our sandbox environment, we must get the AlgoBet repository.
Open a terminal from the root folder and clone the algobet repository to accomplish this:
git clone https://github.com/n-elia/algobet.git

The repository content will be added to the newly formed algobet directory. All the files required to set up and deploy an AlgoBet DApp can be found there: The project documentation is located in *docs/, while the source code for the Smart Contract is located in *src/.
Smart Contract source code is stored in the following directories: src/teal/, src/test/, src/contract.py, src/requirements.txt, and README.md. Readme files are also stored in the src/requirements.txt and src/contract.py directories.

Now open a terminal and enter the following commands to install the prerequisites listed in the.txt file in the src folder:

cd algobet/src/

pip install -r requirements.txt

Your Python environment is now prepared for the deployment of an AlgoBet DApp.


6. Specify the event
We need to define an event at this point. Then, we'll appropriately instantiate an AlgoBet dApp to wager on the specified event.


To create the AlgoBet dApp, we will need: - An Algorand account that will function as the dApp's manager.
- An Algorand account that will act as the dApp's oracle.
- The Unix timestamp corresponding to the event's start time.
- The Unix timestamp corresponding to the event's end time.
- The shortest length of time provided for winning players to claim their prizes.

Assume we want to create a betting system for the soccer game between Catanzaro and Crotone, which begins five minutes after the linked AlgoBet dApp is created and lasts for ten minutes. Consider as well that we want to provide the winners at least 15 minutes after the game is over to request their compensation.

The Algorand accounts that we established and paid in the earlier steps will be used.
We can quickly pop the accounts from the sandbox in a Python script by utilizing beaker.sandbox. Please take note that sandbox.get_accounts() produces a list of sandbox accounts in a specific order. To maintain consistency, we will always pop the accounts in the same order inside the code snippets of the subsequent tutorial steps:ts to

from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")


The management account will be the first one to pop up; the oracle account will be the second; and the final two accounts will be two different participant accounts. The address of each account will be used to identify it.

7. Launch AlgoBet as manager
Our AlgoBet dApp, connected to the soccer match between Catanzaro and Crotone, is now prepared for deployment.

Our dApp will be released using Beaker. Together, let's create a Python script.

We must first retrieve sandbox accounts as described in the preceding steps:
from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")


The Beaker's ApplicationClient (docs) is then instantiated, giving us a quick means to communicate with AlgoBet. The Manager account will be used to sign any operations for the customer.

from beaker.client import ApplicationClient

from contract import AlgoBet

# Create an Application client
app_client_manager = ApplicationClient(
    # Use the `algod` client connected to sandbox
    client=sandbox_client,
    # Provide an AlgoBet instance to the client
    app=AlgoBet(),
    # Select the Manager account as transaction signer
    signer=manager_acct.signer
)

We may now submit an Application Call of type by invoking the ApplicationClient.create() function. To construct the AlgoBet application, generate a transaction that is signed by the Manager account. The event determines which arguments to pass to the method.

import time
from beaker import consts

# Unix timestamp of the event start, computed as 5minutes since code execution
event_start_unix_timestamp = int(time.time() + 5 * 60)
# Unix timestamp of the event end, computed as 10 minutes since event start
event_end_unix_timestamp = event_start_unix_timestamp + 10 * 60
# Minimum time that participants are allowed for requesting the payout, set to 15 minutes
payout_time_window_s = int(15 * 60)

# Create the application on chain using the Application Client signed by Manager account
app_id, app_addr, tx_id = app_client_manager.create(
    # Address of the Manager account
    manager_addr=manager_acct.address,
    # Address of the oracle  account
    oracle_addr=oracle_acct.address,
    # Unix timestamp of the event start
    event_start_unix_timestamp=event_start_unix_timestamp,
    # Unix timestamp of the event end
    event_end_unix_timestamp=event_end_unix_timestamp,
    # Minimum time that participants are allowed for requesting the payout
    payout_time_window_s=payout_time_window_s
)

# Print the Application ID, the Application account address and the ID of creation transaction
print(f"Created AlgoBet dApp with id: {app_id} and address: {app_addr} in tx: {tx_id}")

# Fund the app account with 1 algo
app_client_manager.fund(1 * consts.algo)

# Print the current application state
print(f"Current application state: {app_client_manager.get_application_state()}")

Open a terminal and type the command: create_algobet_dapp.py to run the Python script.

$> python create_algobet_dapp.py

The output of the script should resemble:
Found 5 accounts into the wallet

Manager account: SKBBUTCFKLPOGWMHLMA2ME3OAR4D2KIXLBXX366R2N7EOBS56QNYWN3SKE
Oracle account: P7LUQ4IIU43LGBHSEMDQ2K7OK75ZMLJC2ACEVX2BUAGCCYOGQIQ3LTJGWM
Participant 1 account: FLD3ATI6H3MTVBVE6X3OF7DJF7PC42Z4XTCVXQ3DMDK6O3NRRCQMGUFDKU
Participant 2 account: DB6U6AAH56T2SIMQVUMI4O3SFKTY2QIU2GAPI3N2PRXTNATWFTYGXJUBKI

Created AlgoBet dApp with id: 144934470 and address: EVTLVHOMFDKGWUJN54TBZB5KIIMVKI3A65T77O6ZWUIX5UXE3YTPN754NI in tx: KO5OMLN34ZVRYBGLN5OM3BP4YX5M26CG5HVBUAIOQWR5WCDHICJQ

Process finished with exit code 0

8. As Participant 1, place a wager.
We can now use the deployed AlgoBet dApp to put our wagers on the soccer match between Catanzaro and Crotone. Let's say that Giovanni, who is Participant 1, believes that the home team will win.

We will use Beaker to interface with the deployed dApp. Together, let's create a Python script.

We must first retrieve sandbox accounts as described in the preceding steps:
from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")

The Beaker's ApplicationClient (docs) is then instantiated, giving us a quick means to communicate with AlgoBet.
This time, the application client must communicate with the deployed AlgoBet instance and be signed by the Participant 1 account.


from beaker.client import ApplicationClient

from contract import AlgoBet

APP_ID = 888  # <-- Paste Application ID here
APP_ADDR = 'XXXXXX'  # <-- Paste Application Account Address here

# Create an Application client signed by Participant 1
app_client_participant_1 = ApplicationClient(
    # Use the `algod` client connected to sandbox
    client=sandbox_client,
    # Provide an AlgoBet instance to the client
    app=AlgoBet(),
    # Provide a deployed AlgoBet dApp ID
    app_id=APP_ID,
    # Select the Participant 1 account as transaction signer
    signer=participant_1_acct.signer
)

A Participant account must opt-in before it may communicate with AlgoBet dApp. An asset transfer with a value of 0 is all that constitutes an opt-in transaction. utilizing the ApplicationClient.opt_in() method, the opt-in call can be automatically made when utilizing the Beaker framework.

We are now prepared to call the ApplicationClient.call() method, which will submit an Application Call transaction to the AlgoBet application signed by the Participant 1 account, after the opt-in process has been completed.

AlgoBet.bet is the transaction for which we are ready to ask that it be executed. A contextual deposit transaction is needed for this transaction, one that transfers 140 milliAlgo as a bet deposit to the dApp account. Therefore, we will use Beaker to create an atomic transaction group.

from algosdk.atomic_transaction_composer import TransactionWithSigner
from algosdk.future import transaction

from beaker import consts

# Opt-in into the dApp
app_client_participant_1.opt_in()

# Place a bet as Participant 1 forecasting a home team win
result = app_client_participant_1.call(
    # Transaction to be requested
    AlgoBet.bet,
    # Bet deposit transaction
    bet_deposit_tx=TransactionWithSigner(
        # Transaction of type PaymentTxn
        txn=transaction.PaymentTxn(
            # Address of the account requesting the transfer
            participant_1_acct.address,
            # Transaction parameters (use suggested)
            app_client_participant_1.client.suggested_params(),
            # Receiver account address
            APP_ADDR,
            # Transfer amount
            140 * consts.milli_algo),
        # Payment transaction signer
        signer=participant_1_acct.signer
    ),
    # Option to bet on: home team win
    opt=0
)

# Output the transaction ID.
print(f"Transaction completed with ID: {result.tx_id}")

The Python script will then create an atomic transaction group using Beaker. Save it as algobet_participant_1_bet.py, open a terminal, and run it by issuing.

$> python algobet_participant_1_bet.py

The output of the script should resemble:

Transaction completed with ID: CTV34P2G5G2DO6VHOM436JTJP6RUBVNIE57MNPHRDX7QRJPFY46Q


Troubleshooting hint: If you see the following error, the event start timestamp has already elapsed and Bets cannot be placed by Participants. You can restart the tutorial with a new AlgoBet instance by going back to the dApp creation stage.

global LatestTimestamp
bytec 13 // "event_start_timestamp"
app_global_get
<
// Event has already started
assert      <-- Error
load 8
gtxns Amount
bytec 14 // "bet_amount"
app_global_get



As Participant 2, place a wager.
Using the same script as Participant 1, we may place a wager as Participant 2. The Application Client will be signed by Participant 2 account rather than Participant 1 account, which is the only change we will make.

We need to retrieve sandbox accounts first:

from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")

We then create an ApplicationClient for a Beaker. This time, the Participant 2 account must sign the application client.

from beaker.client import ApplicationClient

from contract import AlgoBet

APP_ID = 888  # <-- Paste Application ID here
APP_ADDR = 'XXXXXX'  # <-- Paste Application Account Address here

# Create an Application client signed by Participant 2
app_client_participant_2 = ApplicationClient(
    # Use the `algod` client connected to sandbox
    client=sandbox_client,
    # Provide an AlgoBet instance to the client
    app=AlgoBet(),
    # Provide a deployed AlgoBet dApp ID
    app_id=APP_ID,
    # Select the Participant 2 account as transaction signer
    signer=participant_2_acct.signer
)

Then, if we choose, we can put a wager. We will force Participant 2 to select Option 1 (Away team wins) instead of Option 1.

from algosdk.atomic_transaction_composer import TransactionWithSigner
from algosdk.future import transaction

from beaker import consts

# Opt-in into the dApp
app_client_participant_2.opt_in()

# Place a bet as Participant 2 forecasting an away team win
result = app_client_participant_2.call(
    # Transaction to be requested
    AlgoBet.bet,
    # Bet deposit transaction
    bet_deposit_tx=TransactionWithSigner(
        # Transaction of type PaymentTxn
        txn=transaction.PaymentTxn(
            # Address of the account requesting the transfer
            participant_2_acct.address,
            # Transaction parameters (use suggested)
            app_client_participant_2.client.suggested_params(),
            # Receiver account address
            APP_ADDR,
            # Transfer amount
            140 * consts.milli_algo),
        # Payment transaction signer
        signer=participant_2_acct.signer
    ),
    # Option to bet on: away team win
    opt=1
)

# Output the transaction ID.
print(f"Transaction completed with ID: {result.tx_id}")

The Python script should be saved as algobet_participant_2_bet.py after which you should launch a terminal and run it by typing:
$> python algobet_participant_2_bet.py

Transaction completed with ID: CTV34P2G5G2DO6VHOM436JTJP6RUBVNIE57MNPHRDX7QRJPFY46Q

Select Oracle as the outcome.
We now have to wait for the event to begin after the bets have been placed. We chose to start the event five minutes after the dApp was created.

Please be aware that Participants are not permitted to place bets after the start of the event, therefore bet transactions that take place after the timestamp for the event start will fail.

Additionally, the Oracle account won't be allowed to set the event outcome prior to the event's conclusion.


Ten minutes after the event's start, we set the timestamp for its conclusion. The Oracle account will be able to set the event result after our AlgoBet dApp considers the event concluded after 15 minutes have passed after its inception.

Assume Catanzaro eventually scored twice to win the match 2-1. Consequently, the best choice isnAnother Python script can be used to set the outcome as Oracle. To enable it to request transactions against the deployed dApp, we will instantiate an Application Client signed by the Oracle account, same to how we did with Participants to force them to bet.

We need to retrieve sandbox accounts first:
from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")

We then create an ApplicationClient for a Beaker. This time, the Oracle account needs to sign the application client.

from beaker.client import ApplicationClient

from contract import AlgoBet

APP_ID = 888  # <-- Paste Application ID here
APP_ADDR = 'XXXXXX'  # <-- Paste Application Account Address here

# Create an Application client signed by Oracle account
app_client_oracle = ApplicationClient(
    # Use the `algod` client connected to sandbox
    client=sandbox_client,
    # Provide an AlgoBet instance to the client
    app=AlgoBet(),
    # Provide a deployed AlgoBet dApp ID
    app_id=APP_ID,
    # Select the Oracle account as transaction signer
    signer=oracle_acct.signer
)

Because the Oracle account won't use the Local State, we don't need to opt-in. We will set up an Oracle account to select the outcome of 0 (Home team wins).

# Set the result "Home team win"
result = app_client_oracle.call(
    # Transaction to be requested
    AlgoBet.set_event_result,
    # Winning option: home team win
    opt=0
)

# Output the transaction ID.
print(f"Transaction completed with ID: {result.tx_id}")

Open a terminal, then issue the command: set_result.py to run the Python script.
$> python set_result.py

Transaction completed with ID: CTV34P2G5G2DO6VHOM436JTJP6RUBVNIE57MNPHRDX7QRJPFY46Q

Request payment as Winner 1 (participant 1).
The winning Participants are now eligible to pick up their prizes.

In this tutorial, Participant 1 and Participant 2 each placed a wager on the outcome of a game: "Home team win" (option 0) and "Away team win" (option 1). As a result, the former will win while the latter will lose.

Let's create a Python script that asks for Participant 1 to receive the reward.
We need to retrieve sandbox accounts first:

from beaker import sandbox

# Get sandbox `algod` client
sandbox_client = sandbox.get_algod_client()

# Retrieve sandbox accounts
wallet_name = "mywallet"  # <-- put wallet's name here
wallet_password = "mywalletpass"  # <-- put wallet's password here
sandbox_accounts = sandbox.get_accounts(
    wallet_name=wallet_name,
    wallet_password=wallet_password,
)
print(f"Found {len(sandbox_accounts)} accounts into the wallet")

# Pop accounts from sandbox
manager_acct = sandbox_accounts.pop()
print(f"Manager account: {manager_acct.address}")

oracle_acct = sandbox_accounts.pop()
print(f"Oracle account: {oracle_acct.address}")

participant_1_acct = sandbox_accounts.pop()
print(f"Participant 1 account: {participant_1_acct.address}")

participant_2_acct = sandbox_accounts.pop()
print(f"Participant 2 account: {participant_2_acct.address}")

We then create an ApplicationClient for a Beaker. This time, the Participant 1 account must sign the application client.

from beaker.client import ApplicationClient

from contract import AlgoBet

APP_ID = 888  # <-- Paste Application ID here
APP_ADDR = 'XXXXXX'  # <-- Paste Application Account Address here

# Create an Application client signed by Participant 1
app_client_participant_1 = ApplicationClient(
    # Use the `algod` client connected to sandbox
    client=sandbox_client,
    # Provide an AlgoBet instance to the client
    app=AlgoBet(),
    # Provide a deployed AlgoBet dApp ID
    app_id=APP_ID,
    # Select the Participant 1 account as transaction signer
    signer=participant_1_acct.signer
)

Now let's make the payout transaction request. To help you see the impact of the payout transaction, the account balance is presented both before and after the payout.

# Query the account balance before payout
participant_1_acct_balance_before = sandbox_client.account_info(participant_1_acct.address)['amount']
print(f"Account balance before requesting the payout: {participant_1_acct_balance_before}")

# Request the payout
result = app_client_participant_1.call(
    # Transaction to be requested
    AlgoBet.payout
)

# Output the transaction ID
print(f"Transaction completed with ID: {result.tx_id}")

# Query the account balance after payout
participant_1_acct_balance_after = sandbox_client.account_info(participant_1_acct.address)['amount']
print(f"Account balance after requesting the payout: {participant_1_acct_balance_after}")

Open a terminal, launch the Python script, and type: payout_participant_1.py to run it.

$> python payout_participant_1.py

The output of the script should resemble:
Account balance before requesting the payout: 7855000
Transaction completed with ID: RHZQQQQ4KPQUCMKFSTPKYNJZJG5J3E2W2BB33TMTK42MMEDN6S5A
Account balance after requesting the payout: 7993000


Summary
Congratulations, you have completed the tutorial.

As of now, you know:

how to build up an Algorand environment for Beaker framework-based Smart Contract development and deployment utilizing the sandbox;
how to make a Beaker Smart Contract into TEAL code;
how to set up a Beaker dApp on the testnet for Algorand;
how to set up a decentralized betting system utilizing the AlgoBet Smart Contract and the Algorand network.
Enjoy using Beaker!
