# signing-apk

To sign an Android app using a keystore in Java, you will need to follow these steps:

1. **Generate a Keystore**: If you don't already have one, generate a keystore using the `keytool` command.

2. **Sign the APK**: Use the `apksigner` tool provided by the Android SDK to sign the APK with the generated keystore.

Here's a step-by-step guide with code snippets:

### Step 1: Generate a Keystore
Use the following command to generate a keystore:
```bash
keytool -genkey -v -keystore my-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias my-alias
```

### Step 2: Sign the APK using `apksigner`

First, ensure you have the Android SDK and `apksigner` tool installed. The `apksigner` tool is located in the `build-tools` directory of your Android SDK.

```bash
/path/to/sdk/build-tools/<version>/apksigner sign --ks my-release-key.jks --out my-app-release-signed.apk my-app-release-unsigned.apk
```

### Step 3: Verify the Signature
To ensure the APK is correctly signed, you can verify the signature:

```bash
/path/to/sdk/build-tools/<version>/apksigner verify my-app-release-signed.apk
```

### Java Code Example

If you want to sign an APK programmatically using Java, you can use the `jarsigner` tool. Here's an example:

```java
import java.io.File;
import java.io.IOException;

public class ApkSigner {
    public static void main(String[] args) {
        String keystore = "my-release-key.jks";
        String alias = "my-alias";
        String unsignedApk = "my-app-release-unsigned.apk";
        String signedApk = "my-app-release-signed.apk";
        String keyPassword = "my-key-password";
        String storePassword = "my-store-password";

        try {
            signApk(keystore, alias, unsignedApk, signedApk, keyPassword, storePassword);
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void signApk(String keystore, String alias, String unsignedApk, String signedApk, String keyPassword, String storePassword) throws IOException, InterruptedException {
        String[] command = {
            "jarsigner",
            "-verbose",
            "-sigalg", "SHA1withRSA",
            "-digestalg", "SHA1",
            "-keystore", keystore,
            "-storepass", storePassword,
            "-keypass", keyPassword,
            unsignedApk,
            alias
        };

        Process process = new ProcessBuilder(command)
                .redirectOutput(new File("jarsigner-output.txt"))
                .redirectError(new File("jarsigner-error.txt"))
                .start();

        int exitCode = process.waitFor();
        if (exitCode == 0) {
            System.out.println("APK signed successfully");
        } else {
            System.out.println("Error occurred while signing APK");
        }
    }
}
```

### Explanation

1. **Keystore Generation**: The `keytool` command generates a keystore file (`my-release-key.jks`) with a specified alias (`my-alias`).

2. **Signing the APK**: The `apksigner` tool signs the APK using the keystore.

3. **Java Code**: The Java code example uses the `jarsigner` command to sign the APK. The command array includes necessary parameters like `-keystore`, `-storepass`, and `-keypass`.

4. **Process Execution**: The `ProcessBuilder` class runs the `jarsigner` command, redirecting output and error streams to files for debugging.

This should cover the basic steps required to sign an Android APK using a keystore in Java.
