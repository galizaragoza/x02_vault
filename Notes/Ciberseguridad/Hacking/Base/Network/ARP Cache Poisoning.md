# Qué es ARP Cache Poisoning
Una vez infiltrado dentro de una red, un atacante puede utilizar los *gratuitous ARP replies* spoofeados, modificando la tabla ARP de la víctima para cambiar su default gateway y que todo el tráfico que iba a enviar al router pase por el atacante, de manera que este lo pueda espiar.
En el caso de la imagen, además el atacante cambia la tabla del router para que este 'piense' que PC-A es el propio atacante, interceptando el tráfico en ambos sentidos.

![[ARP-Cache-poisoning.png|728x442]]