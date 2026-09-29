# Answers to Part 3

Add your answers to the questions in Part 3, Step 2 below. 

## Vulernability Remediation:
### Vulnerability 1: 
1. Which package or library are you addressing?
Pillow 9.4.0, a Python image-processing library listed in `requirements.txt` (line 20). The Trivy scan (`trivy-results.sarif`) flagged it as CRITICAL severity.

2. Which CVE is linked to this vulnerability?
CVE-2023-50447. In Pillow versions up to 10.1.0, the `ImageMath.eval` function can be tricked into running Python code supplied by an attacker, bypassing its `environment` restrictions. This is arbitrary code execution (ACE): if the app passes user input to this function, an attacker could run commands on the server.

3. What remediation steps do you suggest?
The recommended remediation is to upgrade Pillow to 10.2.0 or later by editing line 20 of `requirements.txt`, then rebuild the Docker image and re-scan to confirm the finding is gone. Then, test the app afterward, since this is a large version jump. The code should also never pass untrusted input to `ImageMath.eval`.

### Vulnerability 2:
1. Which vulnerability are you addressing?
PyYAML 5.1, a Python library for reading YAML files, listed in `requirements.txt` (line 29). The Trivy scan (`trivy-results.sarif`) flagged it as CRITICAL severity.

2. Which CVE is linked to this vulnerability?
CVE-2019-20477. In PyYAML 5.1 through 5.1.2, the `FullLoader` option can be tricked by a crafted YAML file into creating Python objects and calling functions. This unsafe deserialization leads to remote code execution (RCE): if the app loads untrusted YAML, an attacker could run commands on the server.

3. What remediation steps do you suggest? 
The recommended remediation is to upgrade PyYAML to 5.4 or later by editing line 29 of `requirements.txt`, then rebuild the image and re-scan. Version 5.2 fixes this CVE, but 5.4+ also fixes CVE-2020-1747 and CVE-2020-14343, which appear in the same scan. In the code, use `yaml.safe_load()` instead of `yaml.load()` or `FullLoader` for untrusted YAML, since it cannot execute code.