生成example.pem & example.avbpubkey
	openssl genrsa -out example.pem 4096
	./avbtool extract_public_key --key example.pem --output example.avbpubkey
	
	# in Android.bp
	apex_key {
		name: "apex.test.key",
		public_key: "example.avbpubkey",
		private_key: "example.pem",
	}



生成example.pk8 & example.x509.pem
	openssl genrsa -3 -out temp.pem 2048
	// openssl req -new -x509 -key temp.pem -out example.x509.pem -days 10000 -subj '/C=US/ST=California/L=San Narciso/O=Yoyodyne, Inc./OU=Yoyodyne Mobility/CN=Yoyodyne/emailAddress=yoyodyne@example.com'
	openssl req -new -x509 -key temp.pem -out example.x509.pem -days 10000 -subj '/C=CN/ST=ShenZhen/L=ShenZhen/O=emdoor/OU=emdoor/CN=emdoor/emailAddress=emdoor@emdoor.com'
	openssl pkcs8 -in temp.pem -topk8 -outform DER -out example.pk8 -nocrypt
	shred --remove temp.pem