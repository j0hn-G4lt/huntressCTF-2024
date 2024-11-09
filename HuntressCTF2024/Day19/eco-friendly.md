## Eco-friendly

```
$encodedFilePath = "eco_friendly"  # File path to eco_friendly
$iteration = 1
$decodedText = Get-Content -Path $encodedFilePath -Raw

while ($true) {
    if ($decodedText -match "iex\s*\(\s*'(.*?)'\s*-f\s+(.*)\)") {
        $formatString = $matches[1]
        $toEvaluate = $matches[2]

        if ($toEvaluate -match '\)$') {
            $toEvaluate = $toEvaluate.Substring(0, $toEvaluate.Length - 1)
        }

        $decodedArguments = $toEvaluate -split ', ' | ForEach-Object {
            Invoke-Expression $_
        }

        $decodedText = $formatString -f $decodedArguments
        Write-Output "Iteration $iteration Output:"
        Write-Output $decodedText
        $iteration++
    } else {
        break
    }
}

Write-Output "Final Decoded Output:"
Write-Output $decodedText
```
