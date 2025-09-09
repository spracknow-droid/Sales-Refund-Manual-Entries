import streamlit as st
import pandas as pd
import plotly.express as px
from streamlit_plotly_events import plotly_events
import io

# Streamlit 페이지 설정
st.set_page_config(
    page_title="매출환입 및 수기입력 내역",
    layout="wide",
)

# 앱의 메인 제목
st.title('매출환입 및 수기입력 내역')

# --- 파일 업로드 섹션 ---
st.sidebar.header('파일 업로드')
uploaded_file = st.sidebar.file_uploader("엑셀 파일을 업로드하세요", type=['xlsx', 'xls'])

# 엑셀 파일이 업로드되었는지 확인
if uploaded_file is not None:
    try:
        # 엑셀 파일을 데이터프레임으로 읽기
        df = pd.read_excel(uploaded_file)

        # 불필요한 컬럼 리스트
        columns_to_drop = [
            'No', '승인번호','비용센터코드','비용센터','프로젝트','프로젝트명','증빙','예산단위','예산계정','회계단위','잔액',
            '작성일','순번','전표유형코드','계정유형','관리항목코드1','관리항목1','관리항목코드2','관리항목2','관리항목코드3','관리항목3',
            '관리항목코드4','관리항목4','관리항목코드5','관리항목5',
            '관리항목코드6','관리항목6','관리항목코드7','관리항목7','관리항목코드8','관리항목8','계정코드'
        ]

        # 불필요한 컬럼 삭제 (오류 발생 시 무시)
        df_cleaned = df.drop(columns=columns_to_drop, errors='ignore')

        # --- 사이드바에 전체 정제 데이터 다운로드 버튼 추가 ---
        st.sidebar.info("데이터 정제가 완료되었습니다. 정제 데이터를 다운로드할 수 있습니다.")

        # 전체 정제 데이터를 엑셀 파일로 생성하여 다운로드 버튼에 연결
        output_cleaned = io.BytesIO()
        with pd.ExcelWriter(output_cleaned, engine='xlsxwriter') as writer:
            df_cleaned.to_excel(writer, index=False, sheet_name='Sheet1')
        xlsx_data_cleaned = output_cleaned.getvalue()

        st.sidebar.download_button(
            label="정제 데이터 다운로드 (XLSX)",
            data=xlsx_data_cleaned,
            file_name='계정별원장_정제_데이터.xlsx',
            mime='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
        )

        # --- 메인 화면에 필터링된 데이터프레임 표시 ---
        if '메뉴' in df_cleaned.columns and '대변' in df_cleaned.columns:
            # 첫 번째 필터링: '메뉴'가 '전표입력'인 데이터
            filtered_groups_1 = ['전표입력']
            df_filtered_1 = df_cleaned[df_cleaned['메뉴'].isin(filtered_groups_1)]

            # 1) 매출환입 데이터 내역: '대변' 컬럼의 값이 0보다 작은 데이터
            df_filtered_2 = df_filtered_1[df_filtered_1['대변'] < 0]

            # 2) 수기입력 매출 내역: '대변' 컬럼의 값이 0보다 큰 데이터
            df_filtered_3 = df_filtered_1[df_filtered_1['대변'] > 0]

            # --- 매출환입 데이터프레임 표시 ---
            st.subheader("1) 매출환입 데이터 내역")
            st.info("일반전표 데이터 중 '대변' 값이 0보다 작은 데이터만 표시됩니다.")
            st.dataframe(df_filtered_2, hide_index=True)

            # 매출환입 데이터 다운로드 버튼
            output_2 = io.BytesIO()
            with pd.ExcelWriter(output_2, engine='xlsxwriter') as writer:
                df_filtered_2.to_excel(writer, index=False, sheet_name='매출환입')
            xlsx_data_2 = output_2.getvalue()
            st.download_button(
                label="매출환입 데이터 다운로드 (XLSX)",
                data=xlsx_data_2,
                file_name='매출환입_데이터.xlsx',
                mime='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
            )

            # --- 수기입력 매출 데이터프레임 표시 ---
            st.subheader("2) 수기입력 매출 내역")
            st.info("일반전표 데이터 중 '대변' 값이 0보다 큰 데이터만 표시됩니다.")
            st.dataframe(df_filtered_3, hide_index=True)

            # 수기입력 매출 데이터 다운로드 버튼
            output_3 = io.BytesIO()
            with pd.ExcelWriter(output_3, engine='xlsxwriter') as writer:
                df_filtered_3.to_excel(writer, index=False, sheet_name='수기입력_매출')
            xlsx_data_3 = output_3.getvalue()
            st.download_button(
                label="수기입력 매출 데이터 다운로드 (XLSX)",
                data=xlsx_data_3,
                file_name='수기입력_매출_데이터.xlsx',
                mime='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
            )

        else:
            st.warning("데이터에 '메뉴' 또는 '대변' 컬럼이 없어 필터링을 적용할 수 없습니다.")
            st.subheader("정제 데이터프레임")
            st.dataframe(df_cleaned)

    except Exception as e:
        st.error(f"파일을 읽거나 처리하는 중 오류가 발생했습니다: {e}")
        st.stop()

# 파일이 업로드되지 않았을 때만 메시지 표시
else:
    st.info('계정별원장 엑셀 파일을 업로드하세요.')
