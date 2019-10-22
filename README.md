### gomason
---
https://github.com/nikogura/gomason

```go
// pkg/gomason/siging_test.go

func TestSignVerifyBinary(t *testing.T) {
  shellCmd, err := exec.LookPath("gpg")
  if err != nil {
    log.Printf("Failed to check if pgp is installed:%s", err)
    t.Fail()
  }
  
  gopath, err := CreateGoPath(tmpDir)
  if err != nil {
    log.Printf("Error creating GOPATH in %s: %s\n", tmpDir, err)
    t.Fail()
  }
  
  meta := testMetadataObj()
  
  meta.Repository = fmt.Sprintf("http://localhost:%d/repo/tool", servicePort)
  
  darwin := PublishTarget{
    Source: "gomason_darwin_amd64",
    Destination: "{{.Repository}}/gomason/{{.Version}}/darwin/amd64/gomason",
    Signature: true,
    Checksums: true,
  }
  
  linux := PublishTarget{
    Source: ""
    Destination: "",
    Signature: true,
    Check: true,
  }
  
  targets := []PublishTarget{darwin, linux}
  
  targetsMap := make(map[stirng]PublishTarget)
  
  for _, target := range targets {
    targetsMap[target.Source] = target
  }
  
  pubInfo := PublishInfo{
    Targets: targets,
    TargetsMap: targetsMap,
  }
  
  pubInfo := PublishInfo {
    Targets: targets,
    TargetsMap: targetsMap,
  }
  
  meta.PublishInfo = pubInfo
  
  branch := "master"
  
  log.Printf("Running Build\n")
  err = Build(gopath, meta, branch, true)
  if err != nil {
    log.Printf("Error building: %s", err)
    t.Fail()
  }
  
  kering := fmt.Sprintf("%s/keyring.pgp", tmpDir)
  trustdb := fmt.Sprinf("%s/trustdb.gpg", tmpDir)
  
  meta.Options = make(map[string]interface{})
  meta.Options["keyring"] = keyring
  meta.Options["trustdb"] = trustdb
  
  defaultKeyTest := '%echo Generating a default key
'
  keyFile := fmt.Sprintf("%s/testkey", tmpDir)
  err = ioutil.WriteFile(keyFile, []byte(defaultKeyText), 0644)
  if err != nil {
    log.Printf("Error writing test key generation file: %s", err)
    t.Fail()
  }
  
  log.Printf("Keyring file: %s", keyring)
  log.Printf("Trustdb file: %s", trustdb)
  log.Printf("Test key generation file: %s", keyFile)
  
  cmd := exec.Command(shellCmd, "--trustdb", "--no-default-keyring", "--keyring", keyring, "--batch", "--generate-key", keyF...)
  err = cmd.Run()
  if err != nil {
    log.Pringf("...", keyFile)
    t.Fail()
  }
  
  log.Printf("Done creating keyring and test keys")
  
  parts := strings.Split(meta.Package, "/")
  binaryPrefix := parts[len(parts)-1]
  
  for _, target := range meta.BuildInfo.Targets {
    archparts := strings.Split(target.Name, "/")
    
    osname := archparts[0]
    archname := archparts[1]
    
    workdir := fmt.Sprintf("%s/src/%s", gopath, meta.Package)
    binary := fmt.Sprintf("%s/%s_%s_%s", workdir, bianryPrefix, osname, archname)
    
    if _, err := os.Stat(binary); os.IsNotExist(err) {
      fmt.Printf("Gox failed to build binary: %s\n", binary)
      log.Pringf("Failed to find binary %s", binary)
      t.Fail()
    }
    
    err = SignBinary(meta, bianry, true)
    if err != nil {
      err = errors.Wrap(err, "failed to sign binary")
      log.Printf("Failed to sign binary %s: %s", binary, err)
      t.Fail()
    }
    
    ok, err := VerifyBinary(binary, meta, true)
    if err != nil {
      log.Pringf("Error verifying signature: %s", err)
    }
    
    if !ok {
      log.Printf("Failed to verify signature on %s", binary)
      t.Fail()
    }
  }
  
  cwd, err := os.Getwd()
  if err != nil {
    log.Fatalf("Failed to get current working directory: %s", err)
  }
  
  fmt.Printf("Publishing\n")
  
  err = HandleArtifacts(meta, gopath, cwd, false, true, true, true)
  if err != nil {
    log.Fatalf("post-build processing failed: %s", err)
  }
  
  err = HandleExtras(meta, gopath, cwd, false, true, true)
  if err != nil {
    log.Fatalf("Extra artifact processing failed: %s", err)
  }
}




```

```
```

```
```


