<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>심쿵 미연시 - 웹 버전</title>
    <style>
        /* CSS 스타일링 */
        body {
            font-family: 'Malgun Gothic', sans-serif;
            background-color: #f4f4f9;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }
        .game-container {
            background-color: #fff;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 600px;
        }
        h1 {
            color: #ff69b4;
            text-align: center;
            margin-bottom: 25px;
        }
        .options-box {
            margin-bottom: 20px;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
        }
        .options-box p {
            font-weight: bold;
            margin-top: 0;
            margin-bottom: 10px;
        }
        .options-box button {
            background-color: #ffb6c1;
            color: white;
            border: none;
            padding: 10px 15px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        .options-box button:hover {
            background-color: #ff85a2;
        }
        #output {
            background-color: #e9ecef;
            padding: 15px;
            border-radius: 8px;
            min-height: 100px;
            white-space: pre-wrap; /* 줄 바꿈 유지 */
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>💖 심쿵 미연시 💖</h1>

        <div id="output">게임을 시작합니다. 성별을 선택해주세요.</div>

        <div id="gender-selection" class="options-box">
            <p>성별:</p>
            <button onclick="selectGender('남')">남자</button>
            <button onclick="selectGender('여')">여자</button>
        </div>

        <div id="self-selection" class="options-box hidden">
            <p id="self-prompt"></p>
            </div>

        <div id="partner-selection" class="options-box hidden">
            <p>이어주고 싶은 사람을 선택해주세요:</p>
            </div>

        <div id="date-question" class="options-box hidden">
            <p id="date-prompt"></p>
            <button onclick="selectDate('카페가기')">카페가기</button>
            <button onclick="selectDate('운동하러 가기')">운동하러 가기</button>
            <button onclick="selectDate('맛집 투어')">맛집 투어</button>
        </div>

        <div id="cafe-event" class="options-box hidden">
            <p>음료수가 나왔네요, 먼저 가지러가시겠어요? (y/n)</p>
            <button onclick="selectCafeEvent('y')">Y (가지러 간다)</button>
            <button onclick="selectCafeEvent('n')">N (가지러 가지 않는다)</button>
        </div>
        
    </div>

    <script>
        // JavaScript 로직
        const girl = ['다현', '채연', '시윤', '연희'];
        const boy = ['태원', '예준', '현석', '민찬'];
        
        let love = 0;
        let playerGender = '';
        let playerName = '';
        let partnerName = '';
        
        const outputDiv = document.getElementById('output');
        const genderDiv = document.getElementById('gender-selection');
        const selfDiv = document.getElementById('self-selection');
        const partnerDiv = document.getElementById('partner-selection');
        const dateQuestionDiv = document.getElementById('date-question');
        const cafeEventDiv = document.getElementById('cafe-event');
        const selfPrompt = document.getElementById('self-prompt');
        
        // --- 헬퍼 함수 ---
        function setOutput(message) {
            outputDiv.innerHTML = message;
        }

        function toggleVisibility(element, show) {
            if (show) {
                element.classList.remove('hidden');
            } else {
                element.classList.add('hidden');
            }
        }

        function createButtons(parentElement, names, handler) {
            parentElement.innerHTML = ''; // 기존 버튼 제거
            parentElement.appendChild(selfPrompt); // 프롬프트 다시 추가
            names.forEach(name => {
                const button = document.createElement('button');
                button.textContent = name;
                button.onclick = () => handler(name);
                parentElement.appendChild(button);
            });
        }

        function getRandomLoveChange() {
            // random.randint(-3, 3) 구현
            return Math.floor(Math.random() * 7) - 3; // 0~6 -> -3~3
        }

        // --- 게임 로직 ---
        
        // 1. 성별 선택
        function selectGender(gender) {
            playerGender = gender;
            toggleVisibility(genderDiv, false);
            toggleVisibility(selfDiv, true);

            if (gender === '남') {
                selfPrompt.textContent = `보기: ${boy.join(' ')} \n마음에 드는 이름을 적어주세요:`;
                createButtons(selfDiv, boy, selectPlayerName);
            } else {
                selfPrompt.textContent = `보기: ${girl.join(' ')} \n마음에 드는 이름을 적어주세요:`;
                createButtons(selfDiv, girl, selectPlayerName);
            }
            setOutput(`당신의 성별은 **${gender}**입니다. 이제 당신의 캐릭터 이름을 선택해주세요.`);
        }

        // 2. 내 이름 선택 (플레이어 캐릭터)
        function selectPlayerName(name) {
            playerName = name;
            toggleVisibility(selfDiv, false);
            toggleVisibility(partnerDiv, true);

            const potentialPartners = playerGender === '남' ? girl : boy;
            createButtons(partnerDiv, potentialPartners, selectPartnerName);

            setOutput(`**${playerName}** 캐릭터를 선택했습니다. 이제 이어주고 싶은 상대방을 선택해주세요.`);
        }

        // 3. 상대방 이름 선택 (공략 캐릭터)
        function selectPartnerName(name) {
            partnerName = name;
            
            // 호감도 랜덤 증감 (파이썬 코드 로직 반영)
            const loveChange = getRandomLoveChange();
            love += loveChange;

            toggleVisibility(partnerDiv, false);
            toggleVisibility(dateQuestionDiv, true);

            const dateOptions = ['카페가기', '운동하러 가기', '맛집 투어'];
            document.getElementById('date-prompt').textContent = `보기: ${dateOptions.join(', ')}\n${partnerName}과 하고 싶은 데이트는 무엇인가요?`;
            
            let message = `**현재 호감도:** ${love} (선택에 따라 ${loveChange} 변동)\n\n`;
            message += `**${partnerName}**: 안녕하세요 ㅎㅎ. 잘 부탁드려요\n\n`;
            message += `${partnerName}이랑 하고 싶은 데이트를 선택해주세요.`;
            setOutput(message);
        }

        // 4. 데이트 선택
        function selectDate(date) {
            setOutput(`**${partnerName}**: 좋은 생각인거 같아요! 얼른 가요 ㅎㅎ`);
            
            if (date === '카페가기') {
                toggleVisibility(dateQuestionDiv, false);
                toggleVisibility(cafeEventDiv, true);
            } else {
                // '운동하러 가기' 또는 '맛집 투어'를 선택한 경우 (현재 파이썬 코드에서는 이벤트 없음)
                // 만약 이 선택지에 대한 추가 로직을 원하시면 여기에 추가할 수 있습니다.
                setOutput(`**${partnerName}**: ${date} 데이트는 즐거웠어요!\n\n현재 호감도: ${love}\n\n다른 이벤트를 추가하려면 코드를 수정해주세요.`);
                toggleVisibility(dateQuestionDiv, false); // 게임 종료 또는 다음 단계로 이동
            }
        }

        // 5. 카페 이벤트 처리
        function selectCafeEvent(choice) {
            let message = '';
            let newLove = love;

            if (choice === 'y') {
                message = `**${partnerName}**: 고마워요, 음료수 가지고 와줘서\n`;
                message += '호감도가 3 증가했습니다.\n';
                newLove += 3;
            } else {
                message = `**${partnerName}**: 음... 그럴 수 있죠\n`;
                message += '호감도가 2 감소하였습니다.\n';
                newLove -= 2;
            }
            
            love = newLove; // 호감도 최종 업데이트
            message += `**현재 호감도는 ${love}입니다.**`;

            setOutput(message);
            toggleVisibility(cafeEventDiv, false); // 이벤트 종료
            // 여기에 게임 재시작 또는 다음 단계 로직 추가 가능
        }

        // 초기 시작 시 설정
        toggleVisibility(selfDiv, false);
        toggleVisibility(partnerDiv, false);
        toggleVisibility(dateQuestionDiv, false);
        toggleVisibility(cafeEventDiv, false);

        // 오류 처리: 파이썬의 '멍청이' 출력은 웹 환경에서 불가능한 이름을 입력했을 때 발생하므로, 버튼 방식으로 대체하여 오류 발생을 방지합니다.

    </script>
</body>
</html>
