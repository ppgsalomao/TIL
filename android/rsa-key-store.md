# Working with Android KeyStore and RSA (API 18+)

Android KeyStore system was included on API 18 providing developers a way to safely store and use encryption keys.

On API 18+, RSA is the only available algorithm, together with PKCS1 padding or no padding.

Later, on API 23+, other options arrived, such as AES and other paddings.

We will split the work with Android KeyStore to encrypt and decrypt using RSA while supporting API 18+ in some steps:
1. Create the Keys and store
1. Encrypt Data
1. Decrypt Data

## Create the Keys and store

To create the keys and store, we have to be carefull with deprecated methods. Because of that, we will need to test for the SDK Version of the device to use the correct way to create the keys.

```
if (Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {
    Calendar start = Calendar.getInstance();
    Calendar end = Calendar.getInstance();
    end.add(Calendar.YEAR, 30);

    KeyPairGenerator kpg = KeyPairGenerator.getInstance("RSA", "AndroidKeyStore");
        kpg.initialize(
            new KeyPairGeneratorSpec.Builder(InstrumentationRegistry.getContext())
                .setAlias("key1")
                .setSubject(new X500Principal("CN=" + "key1"))
                .setSerialNumber(BigInteger.TEN)
                .setStartDate(start.getTime())
                .setEndDate(end.getTime())
                .build());
    kpg.generateKeyPair();
} else {
    KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance(KeyProperties.KEY_ALGORITHM_RSA, "AndroidKeyStore");
        keyPairGenerator.initialize(
            new KeyGenParameterSpec.Builder(
                "key1",
                KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
                .setDigests(KeyProperties.DIGEST_SHA256, KeyProperties.DIGEST_SHA512)
                .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_RSA_PKCS1)
                .setBlockModes(KeyProperties.BLOCK_MODE_ECB)
                .build());
    keyPairGenerator.generateKeyPair();
}
```

## Encrypt Data

To encrypt data, we will need the Public Key. Because of a known bug on API 23, we need to do an extra effort while retrieving the Public Key. But unlike creating the keys, while retrieving them we do not need to worry about the device SDK Version.

```
KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
keyStore.load(null);
PublicKey publicKey = keyStore.getCertificate("key1").getPublicKey();
publicKey = KeyFactory.getInstance(publicKey.getAlgorithm()).generatePublic(new X509EncodedKeySpec(publicKey.getEncoded()));

Cipher c = Cipher.getInstance("RSA/ECB/PKCS1Padding");
c.init(Cipher.ENCRYPT_MODE, publicKey);
byte[] encodedBytes = c.doFinal(theTestText.getBytes());
```

## Decrypt Data

Decrypting data is easier, as there is no workarounds or SDK Version checks.

```
KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
keyStore.load(null);
PrivateKey privateKey = keyStore.getKey("key1", null);
Cipher c = Cipher.getInstance("RSA/ECB/PKCS1Padding");
c.init(Cipher.DECRYPT_MODE, privateKey);
byte[] decodedBytes = c.doFinal(encodedBytes);
String decrypted = new String(decodedBytes);
```