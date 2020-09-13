all: PDS_client.c PDS_server.c  transceiver.c unpcode.c unpheaders.h
	gcc -O3 -c unpcode.c
	gcc -O3 -c PDS_client.c
	gcc -O3 -c PDS_server.c
	gcc -O3 -c transceiver.c
	gcc -O3 PDS_client.o unpcode.o -o PDS_client.out
	gcc -O3 PDS_server.o unpcode.o -o PDS_server.out
	gcc -O3 transceiver.o unpcode.o -o transceiver.out

clean:
	-rm *.out
	-rm *.o
