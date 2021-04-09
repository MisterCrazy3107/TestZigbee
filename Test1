#include <stm32f10x_lib.h>

void Delai(int t)   /* Fonction qui sert à faire perdre son temps au STM32F100RB */
{
	int k;
	for(k = 0; k < (100 * t); k++);
}


void ConfigurerPortC()
{
	RCC->APB2ENR |= (1 << 4);
	GPIOC->CRH = ((GPIOC->CRH & 0x00000000) | (0x00000033));
	GPIOC->CRL = ((GPIOC->CRL & 0x00000000) | (0x30000000));
}


void ConfigurerUSART1()
{
	RCC->APB2ENR |= 0x00000001;   /* Activation de l'horloge pour les fonctions d'entrées-sorties alternatives */
	AFIO->MAPR &= ~(1 << 2); /* Remise à zéro du USART 1 remap */

	RCC->APB2ENR |= 0x00000004;   /* Activation de l'horloge du port GPIOA */
	GPIOA->CRH = ((GPIOA->CRH & 0xFFFFF00F) | (0x000004B0));   /* USART 1 Tx se situe sur la broche PA9,
	qui doit être configurée en « Alternate Output Push-Pull »,
	USART 1 Rx se situe sur la broche PA10,
	qui doit être configurée en « Input Floating » */

	RCC->APB2ENR |= 0x00004000;   /* Activation de l'horloge de l'USART 1 */

	USART1->BRR = 8000000 / 115200;   /* USART1->BRR = 0b1000100* : USARTDIV 1 = 4 + (5 / 16) = 4,3125 */

	USART1->CR1 = 0;
	USART1->CR2 = 0;
	USART1->CR3 = 0;
	USART1->CR1 |= 0x0000000C;   /* Activation de l'émetteur et du récepteur */

	USART1->CR1 |= 0x00002000;   /* Activation de l'USART 1 */
}

int ObtenirOctetRecu()   /* Fonction qui attend que l'USART 1 ait reçu une donnée pour renvoyer cette donnée */
{
	while (!(USART1->SR & USART_FLAG_RXNE));   /* On attend que l'USART 1 signale la réception d'un octet */
	return ((int)(USART1->DR & 0x1FF));   /* L'octet que l'USART 1 a reçu
se trouve dans le registre « USART1->DR » */
}

void EnvoyerOctet(unsigned char donnee)   /* Permet d'envoyer un octet par l'USART 1 */ 
{
	while (!(USART1->SR & USART_FLAG_TXE));   /* On attend que l'USART 1 soit prêt à envoyer une donnée */

	USART1->DR = donnee;   /* L'envoi de 'octet par l'USART 1 démarre
quand on écrit l'octet dans le registre « USART1->DR » */
}

void EnvoyerPlusieursOctets(unsigned char *paquet)   /* Permet d'envoyer une chaîne de caractères par l'USART 1 */
{
while(*paquet != '\0')   /* Tant que l'on est pas arrivé à la fin de la chaîne de caractères */
	{
		EnvoyerOctet(*paquet);   /* On envoie le caractère courant de la chaîne de caractères */
		paquet++;
	}
}

int main(void)
{
	ConfigurerPortC();   /* Configuration des broches reliées à la LED verte et à la LED bleue */

	ConfigurerUSART1();   /* Configuration de l'USART 1 : 115200 bauds */
	EnvoyerPlusieursOctets("Bonjour !\r");   /* Envoi d'un message pour montrer
que la communication est fonctionnelle dans le sens STM32F100RB -> Ordinateur via les modules XBee */
	while(1)
	{
		if(ObtenirOctetRecu() == '1')
		{
			GPIOC->ODR |= (1 << 8);   /* Allumage de la LED bleue */

			EnvoyerPlusieursOctets("LED bleue allumée !\r");   /* Envoi d'un accusé de réception
de la demande d'allumage de la LED bleue */
			Delai(10000);
		}
		if(ObtenirOctetRecu() == '0')
		{
			GPIOC->ODR &= ~(1 << 8); /* Extinction de la LED bleue */

			EnvoyerPlusieursOctets("LED bleue éteinte !\r");   /* Envoi d'un accusé de réception
de la demande d'extinction de la LED bleue */
			Delai(10000);
		}
	}
}

void SystemInit (void)
{ 

}
