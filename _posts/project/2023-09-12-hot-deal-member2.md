---
layout: post
title: "[Hot-Deal #2] SHA-256 암호화 알고리즘으로 비밀번호 암호화"
subtitle: "sha-256 알고리즘을 이해하고 활용합니다."
categories: devlog
tags: troubleshooting sha-256 password
---

> SHA-256 알고리즘을 이해하고 활용합니다.

<!--more-->

## 🌱 SHA-256 암호화 알고리즘으로 비밀번호 암호화

기본적으로 비밀번호를 DB에 저장할 때는 암호화하여 저장합니다. `단순히 보안을 위해` 라고 표현할 수 있지만 2가지 정도의 이유를 설명드리고자 합니다.

첫번째로는 회사도 해당 유저의 비밀번호를 알 수 없어야합니다. 회사 내의 정책으로도 암호화를 사용하는 것을 권장할 수 있겠습니다. 어딘가에서 들려오는 이야기로는 유저가 가지고 있는 웬만한 
개인정보(주소, 휴대번호 등) 또한 암호화하여 저장한다는 이야기를 들은 적이 있습니다. 회사 내에서 개인의 개인정보를 알 수 없도록 DB에 개인정보를 암호화하여 저장한다고 합니다.

두번째로는 해킹에 대한 방지입니다. 일어나선 안되는 일이지만, 회사도 해킹의 대상이 되어 불시에 회원정보가 유출될 수 있습니다. 일반적으로 사람들은 비밀번호를 비슷하게 구성하여 사용하는 경향이 
많기 때문에 한 번 평문으로 된 비밀번호가 유출되면 다른 도메인에서도 그 비밀번호를 활용한 해킹의 위험에 노출될 수 있습니다.

이러한 이유 2가지를 가지고 이번 프로젝트에서는 비밀번호 정도로 암호화를 진행하여 저장하도록 스터디하였습니다.

```java
public class Sha256Util {
  private static final String ENCRYPTION_TYPE = "SHA-256";

  public static EncryptedSourceDto getEncryptDto(String source) {
    String salt = generateSalt();
    String encryptedSource = getEncryptedSourceWithSalt(source, salt);

    return EncryptedSourceDto.builder()
            .encryptedSource(encryptedSource)
            .salt(salt)
            .build();
  }

  public static String getEncryptedSourceWithSalt(String source, String salt) {
    try {
      MessageDigest md = MessageDigest.getInstance(ENCRYPTION_TYPE);

      byte[] byteData = md.digest(source.concat(salt).getBytes());

      return IntStream.range(0, byteData.length)
              .mapToObj(i -> String.format("%02x", byteData[i]))
              .collect(Collectors.joining());
    } catch (NoSuchAlgorithmException e) {
      throw new RuntimeException("SHA-256 암호화 에러");
    }
  }

  private static String generateSalt() {
    ThreadLocalRandom random = ThreadLocalRandom.current();

    byte[] salt = new byte[8];
    random.nextBytes(salt);

    StringBuilder sb = new StringBuilder();
    for (byte b : salt) {
      sb.append(String.format("%02x", b));
    }

    return sb.toString();
  }
}

```

비밀번호 암호화 알고리즘으로는 SHA-256 알고리즘을 선택하였습니다. SHA-256은 단방향 암호화 알고리즘으로 복호화가 불가능하다는 특징을 가지고 있습니다. 
하지만 비밀번호를 클라이언트에서 직접 받아서 무언가를 하는 동작은 존재하지 않을 뿐더러, 복호화할 수 있는 키를 외부에 제공하는 것은 위험부담이 크다고 생각하여 
단방향 암호화 알고리즘으로 비밀번호를 암호화하기에 충분하다고 판단하였습니다.

여기서 발생할 수 있는 문제점이 존재합니다. 단순히 비밀번호 하나를 아무런 추가적인 처리없이 해싱하게 된다면, 같은 비밀번호를 설정한 유저들의 암호화된 비밀번호는 
동일할 것입니다. 이를 통해 해커가 `레인보우 어택`, `브루트 포스 어택` 등의 방법을 통해 비밀번호가 유출될 가능성이 높습니다.

> 레인보우 어택 : 해커들이 미리 지정해놓은 다이제스트를 활용하여 해싱된 비밀번호를 복호화하는 방법
> 브루트 포스 어택 : 무차별적으로 모든 문자를 한번식 대입해서 비밀번호를 풀어내는 방법

이런 SHA-256의 단점을 보완하기 위해 salt라는 것을 추가시킵니다. salt는 인증하고자 하는 비밀번호에 무작위의 문자열을 합하여 함께 암호화한 방식입니다. 
같은 "12345"라는 문자를 암호화하더라도 1번 "12345"에 "a"라는 문자를 추가하여 암호화하는 것과 "b"라는 문자를 추가하여 암호화하는 것이 다르듯이 salt는 
같은 비밀번호라할지라도 서로 다르게 암호화하여 DB에 저장할 수 있도록 합니다. 물론 유저마다 가질 salt는 회원가입시 함께 생성하여 DB에 저장하는 것으로 설계하였습니다.

아래 블로그에서 자세하게 설명되어 있어 자세한 설명은 생략합니다.

##### 🧾 참고 Reference
- [패스워드의 암호화와 저장 - Hash(해시)와 Salt(솔트)](https://st-lab.tistory.com/100)
