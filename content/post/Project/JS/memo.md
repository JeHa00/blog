
```js
import {getElement ,setFetchData} from './common.js';

window.onload = function () {
    const IMP = window.IMP;
    IMP.init('imp62676076');
    const paymentButton = getElement('.order')
    paymentButton.addEventListener('click', (e) => {
        const amount = parseFloat(getElement(".monthly .wrapper input[name='amount']").value.replace(',', ''));
        const type = 'card';
        const merchant_id = passMerchantId(e, amount, type);
        console.log(merchant_id)
        if (merchant_id !== '' && merchant_id !== undefined) {
            IMP.request_pay({
                pg: 'html5_inicis',
                merchant_uid: merchant_id,
                name: 'Devket Premium 서비스',
                pay_method: 'card',
                amount: amount,
            },
            // 결제창 오픈
            function (response) {
                // 결제한 유저의 이름과 이메일 확인하고 결제 버튼 누른 후, 진행되는 로직 
                if (response.success) {
                    let msg = '결제가 완료되었습니다.';
                    msg += '고유ID : ' + response.imp_uid;
                    msg += '상점 거래ID : ' + response.merchant_uid;
                    msg += '결제 금액 : ' + response.paid_amount;
                    storeImpIdInDB(e, response.merchant_uid, response.imp_uid, response.paid_amount, response.status);
                } else {
                    makeStatusFailure(e, response.merchant_uid);
                    let msg = '결제에 실패하였습니다. \n';
                    msg += '에러내용: ' + response.error_msg;
                    alert(msg)
                }
            });
        }
        return false;
    })
};