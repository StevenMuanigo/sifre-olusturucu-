Şifre oluşturucu


``csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Cryptography;
using System.Text;

public class PasswordGenerator
{
    private static readonly char[] UpperCaseLetters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".ToCharArray();
    private static readonly char[] LowerCaseLetters = "abcdefghijklmnopqrstuvwxyz".ToCharArray();
    private static readonly char[] Digits = "0123456789".ToCharArray();
    private static readonly char[] SpecialCharacters = "!@#$%^&*".ToCharArray();

    public enum StrengthLevel
    {
        Weak,
        Medium,
        Strong
    }

    public string GeneratePassword(int length, bool includeUpperCase, bool includeLowerCase, bool includeDigits, bool includeSpecialChars, bool preventRepeating, bool useWords = false, StrengthLevel strength = StrengthLevel.Strong)
    {
        if (length < 8 || length > 64)
        {
            throw new ArgumentException("Password length must be between 8 and 64 characters.");
        }

        var charPool = new List<char>();
        if (includeUpperCase) charPool.AddRange(UpperCaseLetters);
        if (includeLowerCase) charPool.AddRange(LowerCaseLetters);
        if (includeDigits) charPool.AddRange(Digits);
        if (includeSpecialChars) charPool.AddRange(SpecialCharacters);

        if (charPool.Count == 0)
        {
            throw new ArgumentException("At least one character type must be selected.");
        }

        var password = new StringBuilder();

        if (useWords)
        {
            password.Append(GenerateWordBasedPassword(length - 4)); // leaving space for something like "-123"
        }
        else if (strength == StrengthLevel.Strong)
        {
            password.Append(EnsureComplexity(length, includeUpperCase, includeLowerCase, includeDigits, includeSpecialChars));
        }
        else
        {
            password.Append(GenerateRandomPassword(charPool, length, preventRepeating));
        }

        return password.ToString();
    }

    private string GenerateWordBasedPassword(int length)
    {
        return "Word1-Word2-123".Substring(0, length); // Placeholder for illustrative purposes
    }

    private string EnsureComplexity(int length, bool includeUpperCase, bool includeLowerCase, bool includeDigits, bool includeSpecialChars)
    {
        var charPool = new List<char>();
        var password = new List<char>();

        if (includeUpperCase)
        {
            password.Add(UpperCaseLetters[GetRandomNumber(UpperCaseLetters.Length)]);
            charPool.AddRange(UpperCaseLetters);
        }

        if (includeLowerCase)
        {
            password.Add(LowerCaseLetters[GetRandomNumber(LowerCaseLetters.Length)]);
            charPool.AddRange(LowerCaseLetters);
        }

        if (includeDigits)
        {
            password.Add(Digits[GetRandomNumber(Digits.Length)]);
            charPool.AddRange(Digits);
        }

        if (includeSpecialChars)
        {
            password.Add(SpecialCharacters[GetRandomNumber(SpecialCharacters.Length)]);
            charPool.AddRange(SpecialCharacters);
        }

        password.AddRange(GenerateRandomPassword(charPool, length - password.Count, false));

        return new string(password.OrderBy(x => GetRandomNumber(int.MaxValue)).ToArray());
    }

    private List<char> GenerateRandomPassword(List<char> charPool, int length, bool preventRepeating)
    {
        var result = new List<char>();
        while (result.Count < length)
        {
            var charIndex = GetRandomNumber(charPool.Count);
            var nextChar = charPool[charIndex];

            if (preventRepeating && result.Contains(nextChar))
            {
                continue;
            }

            result.Add(nextChar);
        }

        return result;
    }

    private int GetRandomNumber(int max)
    {
        using (var rng = RandomNumberGenerator.Create())
        {
            var randomNumber = new byte[4];
            rng.GetBytes(randomNumber);
            return BitConverter.ToUInt32(randomNumber, 0) % max;
        }
    }
}
```
