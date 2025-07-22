# interfaceSD

to interface sd with esp 32 s3 using spi


#include <stdio.h>
#include "FS.h"
#include "SD.h"
#include "SPI.h"

#define chip_s 10
#define switch 2

const uint8_t level_val = 25,level_val1=21;

/**
 *@function createDir
 *@return void
 *@param void
 *@description 
 */
void createDir(FS fs, const char * path){
  Serial.printf("Creating Dir: %s\n", path);
  if(fs.mkdir(path)){
    Serial.println("Dir created");
  } else {
    Serial.println("mkdir failed");
  }
}

/**
 *@function removeDir
 *@return void
 *@param void
 *@description 
 */
void removeDir(FS fs, const char * path){
  Serial.printf("Removing Dir: %s\n", path);
  if(fs.rmdir(path)){
    Serial.println("Dir removed");
  } else {
    Serial.println("rmdir failed");
  }
}

/**
 * @function readFile
 *@return void
 *@param void
 *@description 
 */
void readFile(FS fs, const char * path){
  Serial.printf("Reading file: %s\n", path);
  
  File file = fs.open(path);
  if(!file){
    Serial.println("Failed to open file for reading");
    return;
  }

  Serial.print("Read from file: ");
  while(file.available()){
    Serial.println(file.read());
  }

  
  file.close();
  Serial.println();
}

/**
 *@function writeFile
 *@return void
 *@param file format, path, data as input
 *@description this
 */
void writeFile(FS fs, const char * path, const uint8_t  message){
  Serial.printf("Writing file: %s\n", path);

  File file = fs.open(path, FILE_WRITE);
  if(!file){
    Serial.println("Failed to open file for writing");
    return;
  }
  if(file.print(message)){
    Serial.println("File written");
  } else {
    Serial.println("Write failed");
  }
  file.close();
}

void removeFile(FS fs, const char * path)
{
  Serial.printf("Removing file: %s\n", path);
  if(fs.remove(path)){
    Serial.println("file removed");
  } else {
    Serial.println("file detect failed");
  }
}

/**
 *@function setup
 *@return void
 *@param void
 *@description 
 */
void setup() {
  // put your setup code here, to run once:

  Serial.begin(115200);
  pinMode(switch, INPUT);
  if(switch==0)
  {
    Serial.println("embedded...");
  }
  if(!SD.begin(chip_s))
  {
    Serial.println("card mount failed.");
    return ;
  }
  uint8_t  cardT = SD.cardType();

  if(cardT==CARD_NONE)
  {
    Serial.println("no sd card");
    return;
  }

   if(cardT == CARD_MMC){
        Serial.println("MMC");
    } else if(cardT == CARD_SD){
        Serial.println("SDSC");
    } else if(cardT == CARD_SDHC){
        Serial.println("SDHC");
    } else {
        Serial.println("UNKNOWN");
    }

  uint32_t cardSize = SD.cardSize() / (1024 * 1024);
  Serial.printf("SD Card Size: %lluMB\n", cardSize);

  createDir(SD, "/mydir");
  removeDir(SD, "/mydir");
 // createDir(SD, "/mydir");
  writeFile(SD, "/hello.txt", level_val);
  writeFile(SD, "/hello1.txt", level_val1);
  readFile(SD, "/hello.txt");
  readFile(SD, "/hello1.txt");
  removeFile(SD,"/hello.txt");


  //Serial.printf("SD Card Size: %lluMB\n", freeKB);

}

/**
 *@function loop
 *@return void
 *@param void
 *@description 
 */
void loop() {
  // put your main code here, to run repeatedly:
  delay(1000); // this speeds up the simulation
}
