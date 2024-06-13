# Test
## **Microcontroleur** 

#### Prerequisites
Before cloning and testing the project, ensure that you have the following prerequisites installed:
 **ENIB Kit**: You need to install the ENIB kit from [here](https://git.enib.fr/bald/mip_install/-/tree/main/fichiers?ref_type=heads) to use certain features of the project.
#### Cloning and Testing Our Project
To clone and test our project, follow these steps:
1-**Clone the Repository**: Open your terminal or command prompt and run the following command to clone the repository to your local machine:
```bash
git clone https://github.com/Eljily-Mohamed/Projet-Emb.git
```
2-**Navigate to Project Directory**: Change your current directory to the project directory by running:

```bash
cd Projet-Emb
```
3-**Make the Project**:you can use the make command to build the project. Run:
```bash
make
```
4- **Launch the Debug File** : You need to launch the debug file. Use the following command to debug the main.elf file Run:
```bash
dbg main.elf
```
5- **Test with st-link** : You can use minicom, gtkterm, or any similar terminal emulator to display the temperature and humidity. Ensure you use the port ttyACM0 and set the baud rate to 115200. You can start minicom with the following command. Run:
```bash
minicom -D /dev/ttyACM0 -b 115200
```
6- **Get Temperature and Humidity** :  After starting minicom, to get the temperature and humidity, type the command:
```bash
commande = t
```
7- **Get color Infos** : After starting minicom, to get the color information such as(temperature (Kelvin) , lux, ..), type the command:
```bash
commande = c
```
Following this guide, you should be able to successfully clone, build, and test our microcontroller project


## **IHM** 

## 🚀 Getting Started
Ces instructions vous permettront d'obtenir une copie du projet en cours d'exécution sur votre machine locale à des fins de développement et de test.
### Prérequis
Avant de commencer, assurez-vous d'avoir Node.js et npm installés sur votre machine.
### Installation
Clonez ce dépôt GitHub sur votre machine locale en utilisant la commande suivante :
```bash
git clone https://github.com/Eljily-Mohamed/emb-projet-ihm.git
```
Accédez au répertoire nouvellement créé :
```bash
cd emb-projet-ihm
```
Installez les dépendances en exécutant la commande suivante :
```bash
npm install
```
## 🔧 Utilisation
Pour lancer l'application, exécutez la commande suivante :
```bash
npm run electron-dev
```