# 중고물품 거래 사이트
JSL 팀 프로젝트

### 개발기간
2018.02 ~ 2018.03 (2개월)

### 팀 구성 및 담당
4명 (기본, 상세설계 및 구현(팀장))

### 주요 언어
- Front-End: HTML, CSS, JavaScript, JQuery
- Back-End: JAVA, JSP/Servlet
- Server: Tomcat 8.5
- DB: Oracle 12c

### 주요 기능
1. 회원 등록, 수정, 탈퇴, 패스워드 찾기(메일)

![3](https://user-images.githubusercontent.com/67365433/104912291-6c006680-59cf-11eb-8df5-dc3ff4a7cd73.png)

2. 상품 등록, 수정, 삭제, 판매&구입 리스트

![5](https://user-images.githubusercontent.com/67365433/104912749-052f7d00-59d0-11eb-80d6-6c3e53afd2cf.png)

3. 관리자 로그인, 관리자의 상품 삭제 및 회원 삭제

![2](https://user-images.githubusercontent.com/67365433/104912131-3c515e80-59cf-11eb-92be-6934d63e38cc.png)

4. 상품리스트, 상품 및 카테고리 검색, 상품 정렬

![4](https://user-images.githubusercontent.com/67365433/104912596-cf8a9400-59cf-11eb-8225-e5e12f8237ac.png)

### 작업 내용
#### 1. 데이터 베이스 설계 및 구현

![1](https://user-images.githubusercontent.com/67365433/104911160-c4366900-59cd-11eb-9961-4da8b807c176.png)


#### 2. 상품리스트 출력

```java
public List<PageVO> PListSelect(PageVO pVo) {
		String sql = "select * from product order by PSDate desc";
		List<PageVO> list = new ArrayList<PageVO>();
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			conn = DBManager.getConnection();
			pstmt = conn.prepareStatement(sql);
			rs = pstmt.executeQuery();

			while (rs.next()) {
				PageVO pVo1 = new PageVO();

				pVo1.setPName(rs.getString("PName"));
				pVo1.setPPhoto(rs.getString("PPhoto"));
				pVo1.setPPrice(rs.getString("PPrice"));
				pVo1.setPSId(rs.getString("PSId"));
				pVo1.setPSDate(rs.getTimestamp("PSDate"));
				pVo1.setPNo(rs.getInt("PNo"));
				pVo1.setPSelled(rs.getInt("PSelled"));
				pVo1.setPClass1(rs.getString("PClass1"));
				pVo1.setPClass2(rs.getString("PClass2"));
				list.add(pVo1);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			DBManager.close(conn, pstmt, rs);
		}
		return list;
	}
```

#### 3. 상품 및 카테고리 검색 및 정렬

[동영상][https://user-images.githubusercontent.com/67365433/105308073-f92a0200-5bff-11eb-8699-fb51d07bc4f0.mp4]


```java		
// 상품 검색할 경우
if (search1 != null) {
	// 이름 순서대로 정렬할 경우
	if (opt.equals("b")) {
	sql = "select * from product where PName like '%" + search1 + "%' order by PName desc";
	// 가격 순서대로 정렬할 경우
	} else if (opt.equals("c")) {
		sql = "select * from product where PName like '%" + search1 + "%' order by TO_NUMBER(PPrice) desc";
	// 날짜 순서대로 정렬할 경우
	} else {
		sql = "select * from product where PName like '%" + search1 + "%' order by PSDate desc";
	}
	// 카테고리별 정렬
	for (String str : category) {
		if (search1.equals(str)) {
			// 이름 순서대로 정렬할 경우
			if (opt.equals("b")) {
				sql = "select * from product where PName like '%" + search1 + "%' or PClass1 like '" + search1
					+ "' or PClass2 like '" + search1 + "' order by PName desc";
			// 가격 순서대로 정렬할 경우
			} else if (opt.equals("c")) {
				sql = "select * from product where PName like '%" + search1 + "%' or PClass1 like '" + search1
					+ "' or PClass2 like '" + search1 + "' order by TO_NUMBER(PPrice) desc";
			// 날짜 순서대로 정렬할 경우
			} else {
				sql = "select * from product where PName like '%" + search1 + "%' or PClass1 like '" + search1
					+ "' or PClass2 like '" + search1 + "' order by PSDate desc";

			}
		}
	}
// 소분류로 검색할 경우
} else if (PClass2 != null) {
	// 이름 순서대로 정렬할 경우
	if (opt.equals("b")) {
		sql = "select * from product where PClass2='" + PClass2 + "' order by PSDate desc";
	// 가격 순서대로 정렬할 경우
	} else if (opt.equals("c")) {
		sql = "select * from product where PClass2='" + PClass2 + "' order by TO_NUMBER(PPrice) desc";
	// 날짜 순서대로 정렬할 경우
	} else {
		sql = "select * from product where PClass2='" + PClass2 + "' order by PSDate desc";
	}
// 대분류로 검색할 경우
} else if (PClass1 != null) {
	// 이름 순서대로 정렬할 경우
	if (opt.equals("b")) {
		sql = "select * from product where PClass1='" + PClass1 + "' order by PSDate desc";
	// 가격 순서대로 정렬할 경우
	} else if (opt.equals("c")) {
		sql = "select * from product where PClass1='" + PClass1 + "' order by TO_NUMBER(PPrice) desc";
	// 날짜 순서대로 정렬할 경우
	} else {
		sql = "select * from product where PClass1='" + PClass1 + "' order by PSDate desc";
	}
// 기본 검색일 경우
} else {
	// 이름 순서대로 정렬할 경우
	if (opt.equals("b")) {
		sql = "select * from product order by PName desc";
	// 가격 순서대로 정렬할 경우
	} else if (opt.equals("c")) {
		sql = "select * from product order by TO_NUMBER(PPrice) desc";
	// 날짜 순서대로 정렬할 경우
	} else {
		sql = "select * from product order by PSDate desc";
	}
}
```

### 보완점
- 예외처리의 부족
- 실제 결제처리